title: Kotlin for Microservices, why?
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->

---

class: impact

# {{title}}

???

- Some of us at TW are very excited about Kotlin and want to share our experience
- Went recently to a meetup in stuttgart about kotlin, had a look at the talks @ Kotlinconf
- Mostly focused on android
- Want to share about our experience using it as a backend language
- We tend to work with microservices a lot, we want to focus on that

---

class: impact center middle

## Mario Fernandez
 Lead Developer
 
 **Thought**Works
 
---

class: transition

# Starting point

???

- want to focus first on my own journey with kotlin

---

class: middle

> For our new service, we are thinking of using something different than Java

???

- java has improved a lot, specially since java 8
- still, 20 years of backwards compatibility

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

???

- surprise!

---

class: transition

# What do you get with Kotlin

???

- we've yet to answer the first question: Why kotlin?

---

class: center middle 

# Lean

???

- Lean is specially useful in the context of microservices, as we aiming to keep things as small as possible

---

class: middle

```kotlin
object CountryLanguageMatrix {
    val locales = mapOf(
            Country("de") to listOf(Language("de_DE")),
            Country("us") to listOf(Language("en_US")),
            Country("ca") to listOf(Language("en_US"), Language("fr_FR")),
            Country("cn") to listOf(Language("zh_CN"), Language("en_GB")),
    )
}
```

???

- DSL capabilities

---

class: middle

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

class: center middle

.img[![effective-java](images/effective-java.jpg)]


???

- for a long time, I've considered effective java THE book about programming
- kotlin addresses many of the points

---

class: center middle

## Item 15: Minimise mutability

---

class: middle

```kotlin
data class Stuff(val value: String)

val safeToShare = listOf(
        Stuff("you"),
        Stuff("won't"),
        Stuff("change"),
        Stuff("this"))
```

???

- Immutability works wonderfully with microservices, as it maps to the domain nicely

---

class: center middle

## Item 17: Design and document for inheritance or else prohibit it

???

- inheritance is easy to abuse
- tightly coupled code that is hard to extract


---

class: middle

```kotlin
class NotExtensible
class BadIdea: NotExtensible() // Error !

open class Extensible {
    open fun say() = "hi"
}

class GoodIdea: Extensible() {
    override fun say() = "hello"
}

```

???

- you can still use inheritance if you want, but by default things are closed
- sane defaults require less explanation, less configuration

---

class: center middle

# Null safety

---

class: middle

# The billion dollar mistake

> I call it my billion-dollar mistake. It was the invention of the null reference in 1965. At that time, I was designing the first comprehensive type system for references in an object oriented language (ALGOL W). My goal was to ensure that all use of references should be absolutely safe, with checking performed automatically by the compiler. But I couldn't resist the temptation to put in a null reference, simply because it was so easy to implement. This has led to innumerable errors, vulnerabilities, and system crashes, which have probably caused a billion dollars of pain and damage in the last forty years.

---

class: center middle

## Pitfalls

---

class: middle

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

class: middle

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

class: center middle

# And much more

???

- there is a lot more to talk about
- json
- testing

---

class: transition

# How can I try it?

???

- all this might sound good, but you still have to convince your PO and your team to give it a try 

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
- If you would transfer complexity from development to ops you would have gained little

---

class: transition

# Kotlin is a gateway drug

???

- Incremental adoption and compatibility can get you hooked up
- Over time, more and more kotlin specific stuff can be embraced

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
