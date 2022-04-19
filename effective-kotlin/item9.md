# Item9: use를 사용하여 리소스를 닫아라

명시적으로 닫아야하는 리소스 : Closeable 인터페이스를 구현(implelmentation) 하고 있다. 
가비지 컬렉터가 처리함 : 느리고, 리소스 유지 비용이 든다.

리소스처리 : try-finally를 이용하여 close 메서드 명시적 호출 > use 함수로 적절히 변경

```kotlin
fun countCharactersInFile(path: String): Int{
    val reader = BufferedReader(FileReader(path))
    try {
        return reader.lineSequence().sumBy { it.length }
    }finally {
        reader.close()
    }
}
```

```kotlin
fun countCharactersInFile(path: String): Int{
    val reader = BufferedReader(FileReader(path))
    reader.use { 
        return reader.lineSequence().sumBy { it.length }
    }
}
```



실제 use 함수의 구현 : 내부적으로 try-catch-finally 뿐만아니라 finally에서 close 할 경우의 exception도 잡아낸다.

```kotlin

/**
 * Executes the given [block] function on this resource and then closes it down correctly whether an exception
 * is thrown or not.
 *
 * @param block a function to process this [Closeable] resource.
 * @return the result of [block] function invoked on this resource.
 */
@InlineOnly
@RequireKotlin("1.2", versionKind = RequireKotlinVersionKind.COMPILER_VERSION, message = "Requires newer compiler version to be inlined correctly.")
public inline fun <T : Closeable?, R> T.use(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    var exception: Throwable? = null
    try {
        return block(this)
    } catch (e: Throwable) {
        exception = e
        throw e
    } finally {
        when {
            apiVersionIsAtLeast(1, 1, 0) -> this.closeFinally(exception)
            this == null -> {}
            exception == null -> close()
            else ->
                try {
                    close()
                } catch (closeException: Throwable) {
                    // cause.addSuppressed(closeException) // ignored here
                }
        }
    }
}
```



useLines 함수 : 파일을 한 줄씩 처리할 때 활용할 수 있음

```kotlin
fun countCharactersInFile(path: String): Int{
  File(path).userLines{ lines -> 
		return lines.sumBy{it.length}}
}
```

메모리에 파일의 내용을 한줄씩만 유지함 : 다만 파일의 줄을 한번만 사용할 수 있다. > 내부적으로 sequence로 되어있다. 

```kotlin
/**
 * Calls the [block] callback giving it a sequence of all the lines in this file and closes the reader once
 * the processing is complete.
 * @return the value returned by [block].
 */
@RequireKotlin("1.2", versionKind = RequireKotlinVersionKind.COMPILER_VERSION, message = "Requires newer compiler version to be inlined correctly.")
public inline fun <T> Reader.useLines(block: (Sequence<String>) -> T): T =
    buffered().use { block(it.lineSequence()) }
```

