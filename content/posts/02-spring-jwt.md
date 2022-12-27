---
title: "JWT token based authentication with Spring Security"
date: 2022-08-17T10:28:30+03:00
expiryDate: 2022-12-01
tags:
  - Spring
  - JWT
  - Tutorial
---

### Intro
I had to set up JWT token-based authentication for our Spring Boot application at work.
While Spring provides a separate library that handles that, there is no detailed documentation and I had to extend some 
classes and do some trial and error to get it working. So I am writing this post as a quick
tutorial on how to get it up and running quickly.

### What is JWT token
JSON Web Token (JWT) is an open standard [RFC 7519](https://tools.ietf.org/html/rfc7519) used to transmit information
between parties in a secure manner. The information is digitally signed usually by using public/private key pair, so 
it can be verified by the receiver.  
[jwt.io](https://jwt.io/) is a useful website to learn more and parse JWT tokens for debugging purposes.

### General flow

First a little explanation on how it works:

![JTW token flow graph](/posts/images/jwt_flow.jpg) 

We have Front-end(FE) and Back-end(BE) parts of our application. When the user opens a page, the FE app will redirect
the user to the login page of the token provider. After a successful login, it will return a JWT token to the FE which
will place it in the Authorization header for each subsequent request to the BE. The BE will verify the token 
and authorize the user.  
In my work we use Azure Active Directory as token provider, but the tutorial is applicable for any other provider.

### Spring Security Setup

Start by adding the Spring Security JWT library. With Maven:
{{< highlight xml >}}
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-jwt</artifactId>
			<version>1.0.11.RELEASE</version>
		</dependency>
{{< / highlight >}}

Before configuring the whole thing, we need to extend 2 classes to put our logic:

**ClaimsVerifier** - we will use this to add any custom verifications we may need. The `verify` method
receives a map of all claims inside the token.

{{< highlight java >}}
@Component
public class ClaimsVerifier implements JwtClaimsSetVerifier {

    @Override
    public void verify(Map<String, Object> claims) throws InvalidTokenException {
        if (!claims.containsKey("scp")) {
            throw new InvalidTokenException("No scp present in token");
        }
    }
}
{{< / highlight >}}

**UserAuthenticationConverter** - to initialize the Spring Security principal from the JWT token. There is 
default implementation `DefaultUserAuthenticationConverter` which uses `UserDetailsService` to get the user, but 
if you need more complex logic, you have to extend it.

{{< highlight java >}}
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.oauth2.provider.token.DefaultUserAuthenticationConverter;
import org.springframework.stereotype.Component;

@Component
public class AzureUserAuthenticationConverter extends DefaultUserAuthenticationConverter {

    private UserService userService;

    @Autowired
    public AzureUserAuthenticationConverter(UserService userService) {
        this.userService = userService;
    }

    @Override
    public Authentication extractAuthentication(Map<String, ?> map) {
        //get user mail(id) from the token, the name may be different depening on the provider
        String email = map.get("unique_name").toString();
        
        //check if the user exists in the app database, otherwise create it
        User user = userService.findByEmail(email.toUpperCase()).orElseGet(() -> createNewUser(map));

        //initialize the principle and its permissions
        return new UsernamePasswordAuthenticationToken(user, "N/A", getAuthority(user));
    }

    private Collection<GrantedAuthority> getAuthority(User user) {
        //setup user permissions
    }
    
    private User createNewUser(Map<String, ?> map) {
        //create new user in the db
    }
}
{{< / highlight >}}

Finally we are ready to build the configuration that ties it together.
Create the initial configuration class and inject the 2 classes we extended above:

{{< highlight java >}}
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableResourceServer;
import org.springframework.security.oauth2.config.annotation.web.configuration.ResourceServerConfigurerAdapter;
import org.springframework.security.oauth2.config.annotation.web.configurers.ResourceServerSecurityConfigurer;
import org.springframework.security.oauth2.provider.token.DefaultAccessTokenConverter;
import org.springframework.security.oauth2.provider.token.DefaultTokenServices;
import org.springframework.security.oauth2.provider.token.TokenStore;
import org.springframework.security.oauth2.provider.token.store.jwk.JwkTokenStore;

@Configuration
@EnableResourceServer
public class OAuth2ResourceServerConfig extends ResourceServerConfigurerAdapter {
        private ClaimsVerifier claimsVerifier;
    
        private AzureUserAuthenticationConverter userAuthenticationConverter;
    
        @Autowired
        public OAuth2ResourceServerConfig(ClaimsVerifier claimsVerifier,
                                          AzureUserAuthenticationConverter userAuthenticationConverter) {
            this.claimsVerifier = claimsVerifier;
            this.userAuthenticationConverter = userAuthenticationConverter;
        }
}
{{< / highlight >}}

Configure Spring Security to require authentication:

{{< highlight java >}}
    @Override
    public void configure(HttpSecurity http) throws Exception {
        http
                .antMatcher("/**")
                .authorizeRequests()
                //use this for development purposes when you need to access BE from FE on different domains
                .antMatchers(HttpMethod.OPTIONS).permitAll()
                .anyRequest()
                    .authenticated();
    }
{{< / highlight >}}

Configure the Token Store and plug in our custom classes:

{{< highlight java >}}
    /** holds the web address where you can access the public keys to verify the token
        this address is for Azure, replace it with yours */
    private final String jwkUrl = "https://login.microsoftonline.com/{tenant_id}/discovery/v2.0/keys";

    @Override
    public void configure(ResourceServerSecurityConfigurer config) {
        config.tokenServices(tokenServices());
        
        //the resource id from you token provider
        config.resourceId("resource_id");
    }

    //just a pass through bean in order to set our custom TokenStore
    @Bean
    @Primary
    public DefaultTokenServices tokenServices() {
        DefaultTokenServices defaultTokenServices = new DefaultTokenServices();
        defaultTokenServices.setTokenStore(tokenStore());
        return defaultTokenServices;
    }
    
    /** create JwkTokenStore which will automatically use the public keys from the provided URL to verify 
        the token and then call our claims verifier and token converter */
    @Bean
    public TokenStore tokenStore() {
        DefaultAccessTokenConverter tokenConverter = new DefaultAccessTokenConverter();
        tokenConverter.setUserTokenConverter(userAuthenticationConverter);
        return new JwkTokenStore(jwkUrl, tokenConverter, claimsVerifier);
    }
{{< / highlight >}}

And this is it, you should be able to authenticate with a valid JWT token.  
Thank you for reading.
