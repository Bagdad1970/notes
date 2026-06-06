Далее в документе под `Filter` понимается интерфейс `jakarta.servlet.Filter` (сюда за подробностями [[Servlets]]).

Безопасность Servlet приложений в Security основана на фильтрах Servlet т.е. Spring Security использует контейнер сервлетов для обеспечения безопасности приложения.

Клиент отправляет запрос приложению, и контейнер сервлетов создает `FilterChain`, который содержит экземпляры `Filter` и `Servlet`, который обрабатывает `HttpServletRequest`.
Сначала запрос проходит через фильтры и, только после успешного прохождения, попадает в сервлет, который обрабатывает его.
Функции фильтров:
- Предотвращение вызова нижестоящих фильтров или сервлета. В этом случае фильтр, как правило, самостоятельно формирует HttpServletResponse. То есть, если запрос не прошел фильтрацию, то экземпляр `Filter` непосредственно формирует ответ, а не `DispatcherServlet`.
- По мере прохождения фильтров, ServletRequest или ServletResponse могут ими модифицироваться.

![[Pasted image 20260401024310.png|294]]


## DelegatingFilterProxy
`DelegatingFilterProxy` (реализация `Filter`), позволяет связать жизненный цикл контейнера сервлетов и `ApplicationContext`. Контейнер сервлетов позволяет регистрировать экземпляры `Filter`, используя свои стандарты, но он не имеет доступа к компонента Spring.

При использовании фильтров сервлетов их необходимо объявить как `filter-class` в web.xml, в противном случае контейнер сервлетов будет игнорировать их. `DelegatingFilterProxy` связывает web.xml и контекст приложения.

Прокси для сервлетного фильтра, который делегирует вызовы бину Spring, реализующему `Filter`.

Обычно в `web.xml` содержится определение `DelegatingFilterProxy`, где указанное в `<filter-name>` имя соответствует имени бина в контексте Spring. Все вызовы к `DelegatingFilterProxy` будут делегированы соответствующему бину в Spring-контексте.

![[Pasted image 20260402014319.png|298]]

`DelegatingFilterProxy` сначала ищет бин `Filter0` в ApplicationContext, а затем вызывает `Filter0`.
Псевдокод `DelegatingFilterProxy`:
```java
void doFilter(
	ServletRequest request,
	ServletResponse response,
	FilterChain chain
) {
	Filter delegate = getFilterBean(someBeanName);  (1)
	delegate.doFilter(request, response);  (2)
}
```
(1) - Получает ленивым образом бин, отвечающий за фильтрацию.
`DelegatingFilterProxy` позволяет отложить поиск бина для фильтрации. Контейнеру сервлетов необходимо зарегистрировать экземпляры фильтров до того, как контейнер запустится. Однако Spring обычно использует `ContextLoaderListener` для загрузки Spring-бинов, и это происходит уже после того, как фильтры должны быть зарегистрированы.

(2) - Полученный bean выполняет фильтрацию запроса.

## FilterChainProxy
`FilterChainProxy` выполняет поддержку сервлетов Spring Security. `FilterChainProxy` - это специальный фильтр, который позволяет делегировать свою работу множеству экземплярам `Filter` через `SecurityFilterChain`. Поскольку `FilterChainProxy` является компонентом, он обычно завернут в `DelegatingFilterProxy`.

`FilterChainProxy` содержит список `SecurityFilterChain`, каждый из которых содержит `RequestMatcher` и список фильтров, которые должны быть применены к соответствующим запросам.


![[Pasted image 20260402015758.png|572]]



### Обработка запросов
Необходимо указать все URI-шаблоны, которые должен обрабатывать `FilterChainProxy`. Для каждого входящего запроса ищется первое совпадение по шаблону. Это означает, что необходимо размещать uri-шаблоны от частных к общим.

`FilterChainProxy` не будет перебирать остальные записи в поисках дополнительных фильтров после того, как совпадение найдено.

Кроме того, `FilterChainProxy` соблюдает стандартное поведение фильтров в отношении вызова `Filter.doFilter()`. Если какой-либо фильтр в выбранной цепочке не вызовет `chain.doFilter()` (например, при ошибке аутентификации), то оставшаяся часть как исходной сервлетной цепочки, так и цепочки фильтров, объявленной в `FilterChainProxy`, вызвана не будет.

## SecurityFilterChain
`SecurityFilterChain` представляет последовательность фильтров, которые проверяют и обрабатывают каждый входящий HTTP-запрос, прежде чем он достигнет контроллеров.

Каждый фильтр имеет одну из следующих задач:
1. Аутентифицировать пользователей
2. Проверять разрешения
3. Обрабатывать токены CSRF
4. Перенаправлять или запрещать несанкционированный доступ

`SecurityFilterChain` используется `FilterChainProxy` для определения того, какие экземпляры `Filter` должны быть вызваны для текущего запроса.

![[Pasted image 20260401033109.png|569]]


Пример SecurityFilterChain:
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(Customizer.withDefaults())
            .httpBasic(Customizer.withDefaults())
            .formLogin(Customizer.withDefaults())
            .authorizeHttpRequests((authorize) -> authorize
                .anyRequest().authenticated()
            );
             
        return http.build();
    }
}
```


**Замечание**: Порядок фильтров в цепочке крайне важен

## Обработка Security исключений
