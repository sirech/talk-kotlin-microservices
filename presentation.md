title: Kotlin for Microservices, why?
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->

---

class: impact

# {{title}}

---

class: impact center middle

## Mario Fernandez
 Lead Developer
 
 **Thought**Works
 
---

class: transition

# Starting point

---

class: middle

> For our new service, we are thinking of using something different than Java

---

picture of jvm langs

???

- many options, I don't have practical experience on these languages

---

picture of kotlin selected

---

class: transition

# What do you get with Kotlin

---

class: center middle 

# Seamless transition

---

![springboot](images/springboot.png)

picture of existing libraries

-> make use of exisitng libraries and frameworks such as springboot, junit, jackson, mockito, assertj, wiremock

---

```Dockerfile
FROM openjdk:8-jre-alpine3.9

WORKDIR /app
EXPOSE 4003

ENV ENV='dev'

RUN apk add --update --no-cache dumb-init \
  && rm -rf /var/cache/apk/*

COPY build/libs/*.jar app.jar

RUN adduser -D runner

USER runner
ENTRYPOINT ["/usr/bin/dumb-init", "--"]
CMD ["java", "-Dspring.profiles.active=${ENV}", "-jar", "app.jar" ]
```

---

class: center middle 

# Lean

???

- Lean is specially useful in the context of microservices, as we aiming to keep things as small as possible

---

```kotlin
object CountryLanguageMatrix {
    val locales = mapOf(
            Country("de") to listOf(Language("de_DE")),
            Country("us") to listOf(Language("en_US")),
            Country("ca") to listOf(Language("en_US"), Language("fr_FR")),
            Country("cn") to listOf(Language("zh_CN"), Language("en_GB")),
    )
}

fun Country.isDefaultLanguage(language: Language): Boolean {
    return CountryLanguageMatrix.locales[this]?.run {
       this[0] == language
    } ?: false
}
```

---

```kotlin
package my.utils

object Utils

fun String.asStream(): InputStream {
    return Utils.javaClass.classLoader.getResourceAsStream(this)
}

fun InputStream.readTextAndClose(charset: Charset = Charsets.UTF_8) = 
    this.bufferedReader(charset).use { it.readText() }
```

```kotlin
import my.utils.asStream
import my.utils.readTextAndClose

val jwt = "jwt".asStream().readTextAndClose()
```

---

class: center middle

# Sane defaults

---

immutability example

---

closed classes & override

---

class: center middle

# Null safety

---

class: center middle

## Pitfalls

---

```kotlin
override fun preHandle(
  request: HttpServletRequest, 
  response: HttpServletResponse, 
  handler: Any): Boolean {
    val value: String = request.getHeader("Header")
    
    // value is technically of type String!
    // It can actually be null
}
```

---

```kotlin
override fun doFilterInternal(
        request: HttpServletRequest,
        response: HttpServletResponse,
        filterChain: FilterChain) {
    request.getHeader(Headers.AUTHORIZATION)?.let { header ->
        jwt(header)?.let { jwt ->
            authentication(jwt)?.let { auth ->
                val context = SecurityContextHolder.getContext()
                context.authentication = auth
            }
        }

    filterChain.doFilter(request, response)
}
```

---

class: transition

# Kotlin is a gateway drug

---

new libraries

---

new paradigms

---

class: transition

# Conclusion

---

class: center middle

![thumbsup](images/thumbsup.png)

---

class: transition

# Links

---

class: middle

- https://hceris.com/painless-json-with-kotlin-and-jackson/
- https://hceris.com/mock-verification-in-kotlin/

---
