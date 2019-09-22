title: Kotlin for Microservices, why?
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->

---

class: impact

# {{title}}

???

- Some of us at TW are very excited about Kotlin
- Want to share about our experience, mostly in the context of microservices

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

???

- That's the beauty of microservices, experiments are possible

---

class: center middle

.image-grid[
.img[![java](images/java.png)]
.img[![scala](images/scala.png)]
.img[![clojure](images/clojure.png)]
.img[![jruby](images/jruby.png)]
]

???

- let's say you want to stay in the jvm
- many options, I don't have practical experience on these languages

---

class: full-width
background-image: url(images/kotlin.png)

---

class: transition

# What do you get with Kotlin

---

class: center middle 

# Seamless transition

---

class: center middle
![springboot](images/springboot.png)

???

- you don't need to change everything at once
- kotlin makes a big point of remaning compatible with java

---

class: center middle

.image-grid[
.img[![junit5](images/junit5.png)]
.img[![wiremock](images/wiremock.png)]
.img[![mockito](images/mockito.png)]
.img[![assertj](images/assertj.png)]
]

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

???

- Operationally, there is little change.
- If you are dockerizing your services, the only change is adding the right dependencies to the jar you build

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

class: center middle 

# New libraries

---

class: center middle 

.image-grid-1-row[
.img[![mockk](images/mockk.png)]
.img[![strikt](images/strikt.png)]
]

---

class: center middle 

# New paradigms

---

class: center middle 

.img[![arrow](images/arrow.png)]

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
