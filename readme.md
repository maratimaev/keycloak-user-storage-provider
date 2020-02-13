# A custom Keycloak User Storage Provider

### Пример реализации адаптера для проверки пользователей из внешнего хранилища.
Сервер аутентификации oauth2 Keycloak

### Для получения JWT токена необходимо сделать POST запрос на keycloak
http://localhost:8180/auth/realms/(realm)/protocol/openid-connect/token
с заполнением полей для x-www-form-urlencoded в Postman
client_id       login-app           (client)
username        Katie.Washington
password        123
grant_type      password
client_secret   1

### Полученный access_token используется для REST запросов 
`{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJyUEY4c1NaTFRqbVBDcktqOVJNdG5OMEN4YUJ3dVJkYTRqTlFNMVNxM2F3In0.eyJqdGkiOiI3MmJhODAzZi1jNzk0LTQ3MGYtYTIwNS01MjdlM2ZmOWFmYWQiLCJleHAiOjE1ODE1ODA0NDMsIm5iZiI6MCwiaWF0IjoxNTgxNTgwMTQzLCJpc3MiOiJodHRwOi8vbG9jYWxob3N0OjgxODAvYXV0aC9yZWFsbXMvU3ByaW5nS2V5Q2xvYWNrIiwiYXVkIjoiYWNjb3VudCIsInN1YiI6ImY6Yzc5NjVjYmEtNDFjNy00NDJhLWI0NmQtZGI5M2YyNzU0MTI2OjEiLCJ0eXAiOiJCZWFyZXIiLCJhenAiOiJsb2dpbi1hcHAiLCJhdXRoX3RpbWUiOjAsInNlc3Npb25fc3RhdGUiOiJlOGM1OTI2ZC01NDllLTRkNjgtYWYyNy05ODEwMTVkZjAxMzUiLCJhY3IiOiIxIiwiYWxsb3dlZC1vcmlnaW5zIjpbIioiXSwicmVhbG1fYWNjZXNzIjp7InJvbGVzIjpbIm9mZmxpbmVfYWNjZXNzIiwidW1hX2F1dGhvcml6YXRpb24iXX0sInJlc291cmNlX2FjY2VzcyI6eyJhY2NvdW50Ijp7InJvbGVzIjpbIm1hbmFnZS1hY2NvdW50IiwibWFuYWdlLWFjY291bnQtbGlua3MiLCJ2aWV3LXByb2ZpbGUiXX19LCJzY29wZSI6ImVtYWlsIHByb2ZpbGUiLCJlbWFpbF92ZXJpZmllZCI6ZmFsc2UsIm5hbWUiOiJLYXRpZSBXYXNoaW5ndG9uIiwicHJlZmVycmVkX3VzZXJuYW1lIjoia2F0aWUud2FzaGluZ3RvbiIsImdpdmVuX25hbWUiOiJLYXRpZSIsImZhbWlseV9uYW1lIjoiV2FzaGluZ3RvbiIsImVtYWlsIjoia2F0aWUud2FzaGluZ3RvbkBleGFtcGxlLmNvbSJ9.dCd75knqKwk-dgAWFI4kGlHfeWmWUxobmiGigiai9R3c0e09_PbsadnUpC4jqQojet3ocOrUXGIndW3IF3W4j5at--bQhkENg8H8fPsXYseHqjQtmeB8JAfskDa64y6vaeOhMkjnBJDN4as-RgEJ3xplk9QI-DrlkI-gSAxgHirk2p-7CcVCfgnedOug9dNXyOw1BqMJ3Q8B54CrFzHiU0d2MNsC20SyMEf8CfT4WiynhjV6h-iAbEiDbgfd265p6wu-mqk2xves3x7wBFyeHK7W3siYIsu-Xo3GGrTrgc_2A5YsR-J1HQk7zjjf5XKiAHxtaHCdDTaGlDej0ShxOg",
    "expires_in": 300,
    "refresh_expires_in": 1800,
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICI4Mzc3OTE1Zi01NzdmLTRkMjktODZlMS1iYWI2YWU1ZDkxN2YifQ.eyJqdGkiOiIzYmE1MWRlYy0yMGQxLTQxMmItYmU5ZS1iMDQzZmZjYTgyMzEiLCJleHAiOjE1ODE1ODE5NDMsIm5iZiI6MCwiaWF0IjoxNTgxNTgwMTQzLCJpc3MiOiJodHRwOi8vbG9jYWxob3N0OjgxODAvYXV0aC9yZWFsbXMvU3ByaW5nS2V5Q2xvYWNrIiwiYXVkIjoiaHR0cDovL2xvY2FsaG9zdDo4MTgwL2F1dGgvcmVhbG1zL1NwcmluZ0tleUNsb2FjayIsInN1YiI6ImY6Yzc5NjVjYmEtNDFjNy00NDJhLWI0NmQtZGI5M2YyNzU0MTI2OjEiLCJ0eXAiOiJSZWZyZXNoIiwiYXpwIjoibG9naW4tYXBwIiwiYXV0aF90aW1lIjowLCJzZXNzaW9uX3N0YXRlIjoiZThjNTkyNmQtNTQ5ZS00ZDY4LWFmMjctOTgxMDE1ZGYwMTM1IiwicmVhbG1fYWNjZXNzIjp7InJvbGVzIjpbIm9mZmxpbmVfYWNjZXNzIiwidW1hX2F1dGhvcml6YXRpb24iXX0sInJlc291cmNlX2FjY2VzcyI6eyJhY2NvdW50Ijp7InJvbGVzIjpbIm1hbmFnZS1hY2NvdW50IiwibWFuYWdlLWFjY291bnQtbGlua3MiLCJ2aWV3LXByb2ZpbGUiXX19LCJzY29wZSI6ImVtYWlsIHByb2ZpbGUifQ.2EQB0_byLMfwz5JSy4TtNFvxywrmTT29HbUQw51V1H8",
    "token_type": "bearer",
    "not-before-policy": 0,
    "session_state": "e8c5926d-549e-4d68-af27-981015df0135",
    "scope": "email profile"
}`


## Deployment

Собранный пакет ear надо скопировать в `standalone/deployments`
Затем добавить провайдер в интерфейсе keycloak - User Federation
Логи в `standalone/log`

## Debug

Для запуска keycloak в режиме дебага необходимо использовать 
`keycloak-8.0.2\bin>standalone.sh -Djboss.socket.binding.port-offset=100 --debug`
порт 8787

## REST config
Для настроки keycloak аутентификации надо добавить в рест клиент 
### в pom.xml

        <dependency>
            <groupId>org.keycloak</groupId>
            <artifactId>keycloak-spring-security-adapter</artifactId>
            <version>8.0.2</version>
        </dependency>
        <dependency>
            <groupId>org.keycloak</groupId>
            <artifactId>keycloak-tomcat7-adapter-dist</artifactId>
            <version>8.0.2</version>
            <type>pom</type>
        </dependency>
        
  <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.keycloak.bom</groupId>
                <artifactId>keycloak-adapter-bom</artifactId>
                <version>8.0.2</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    
### В SecurityConfig.java

@KeycloakConfiguration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class KeycloakSecurityConfig extends KeycloakWebSecurityConfigurerAdapter {

    private final KeycloakClientRequestFactory keycloakClientRequestFactory;

    public KeycloakSecurityConfig(KeycloakClientRequestFactory keycloakClientRequestFactory) {
        this.keycloakClientRequestFactory = keycloakClientRequestFactory;

        // to use principal and authentication together with @async
        SecurityContextHolder.setStrategyName(SecurityContextHolder.MODE_INHERITABLETHREADLOCAL);
    }

    //
    @Bean
    @Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
    public KeycloakRestTemplate keycloakRestTemplate() {
        return new KeycloakRestTemplate(keycloakClientRequestFactory);
    }

    public SimpleAuthorityMapper grantedAuthority() {
        SimpleAuthorityMapper mapper = new SimpleAuthorityMapper();
        mapper.setConvertToUpperCase(true);
        return mapper;
    }

    //
    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) {
        KeycloakAuthenticationProvider keycloakAuthenticationProvider = keycloakAuthenticationProvider();
        keycloakAuthenticationProvider.setGrantedAuthoritiesMapper(grantedAuthority());
        auth.authenticationProvider(keycloakAuthenticationProvider);
    }

//    @Autowired
//    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
//        auth.authenticationProvider(keycloakAuthenticationProvider());
//    }

    @Bean
    @Override
    protected SessionAuthenticationStrategy sessionAuthenticationStrategy() {
        return new RegisterSessionAuthenticationStrategy(new SessionRegistryImpl());
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        super.configure(http);
        ExpressionUrlAuthorizationConfigurer<HttpSecurity>.ExpressionInterceptUrlRegistry expressionInterceptUrlRegistry = http.cors()
                .and() //
                .csrf().disable() //
                .anonymous().disable() //
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS) //
                .and() //
                .antMatcher("/**")
                .authorizeRequests()
                .antMatchers("/", "/login**","/callback/", "/webjars/**", "/error**")
                .permitAll()
                .anyRequest()
                .authenticated();
    }
}

### в src/main/webapp/WEB-INF/keycloak.json
выгружен из Clients->login-app->Installation

{
  "realm": "SpringKeyCloack",
  "auth-server-url": "http://localhost:8180/auth/",
  "ssl-required": "external",
  "resource": "login-app",
  "public-client": true,
  "confidential-port": 0
}
