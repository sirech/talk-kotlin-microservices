title: What we learnt building a microservice in Kotlin
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

# Context

---

class: full-height
background-image: url(images/scheme.jpg)

---

class: center middle 

![springboot](images/springboot.png)

---

class: middle

# Why switch?

- Immutability

--

- Null safety

--

- Compactness

---

class: impact

# Let's see some examples

---

class: transition

# Pseudo DSLs

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

class: transition

# Extension functions

---

```kotlin
package my.utils

object Utils

fun String.asStream(): InputStream {
    return Utils.javaClass.classLoader.getResourceAsStream(this)
}

fun InputStream.readTextAndClose(charset: Charset = Charsets.UTF_8): String {
    return this.bufferedReader(charset).use { it.readText() }
}

```

---

```kotlin
import my.utils.asStream
import my.utils.readTextAndClose

val jwt = "jwt".asStream().readTextAndClose()
```

---

class: transition

# JSON

---

```kotlin
data class UserId(private val value: String) {
    companion object {
        @JvmStatic
        @JsonCreator
        fun create(value: String) = UserId(value.toLowerCase())
    }

    @JsonValue
    override fun toString() = value
}
```

---

```kotlin
class User(
  id: UserId, 
  name: String
)
```

```json
{
  "id": "33242123",
  "name": "The Dude"
}
```

---

class: transition

# Testing

---

.col-12[
![junit](images/junit5.png)
]
.col-6[
![mockk](images/mockk.png)
]
.col-6[
![atrium](images/attrium.svg)
]

---

# Meaningful test names

```kotlin
@Test
fun `is case insensitive`() {
}
```

---

# Fluent declarations

```kotlin
val car = mockk<Car>()

every { car.drive(Direction.NORTH) } returns Outcome.OK
car.drive(Direction.NORTH) // returns OK

verify { car.drive(Direction.NORTH) }
```

---

# Capturing arguments

```kotlin
@Test
fun `injects header into the request and passes it to the filter`() {
    filter.doFilter(request, response, filterChain)

    slot<ServletRequest>().let { slot ->
        verify { filterChain.doFilter(capture(slot), response) }

        expect(slot.captured).isA<HttpServletRequestWrapper> {
            expect(subject.getHeader(Headers.EXTRA_HEADER))
              .toBe("value")
        }
    }
}
```

---

class: transition

# Null Safety

---

```kotlin
@RestController
class Controller(
  @Autowired val gateway: Gateway) {

  @GetMapping(URL)
  fun fetch(@PathVariable("userId") id: UserId): 
    ResponseEntity<User> {
      return gateway.fetch(id)?.run {
        ResponseEntity.ok(this)
      } ?: ResponseEntity.notFound()
  }
}
```

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

class: transition

# Conclusion

---

class: center middle

## Quick transition

---

class: center middle

## Productivity boost

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

---
