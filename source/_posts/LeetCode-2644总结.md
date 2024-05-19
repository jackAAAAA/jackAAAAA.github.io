---
title: LeetCode_2644总结
abbrlink: 7942b5c7
categories: 算法
tags:
  - LeetCode
  - Kotlin
  - Python
description: 一篇对于LeetCode_2644.找出可整除性得分最大的整数；个人与chatGPT4o讨论沉淀以及kotlin & python Tricks
date: 2024-05-19 11:55:23
cover:
---

# Kotlin
```kotlin
// Ways_1_1
Class Solution {
    fun maxDivScore(nums: IntArray, divisors: IntArray): Int {
            // Create a list to store pairs of (index, divisor)
        val indexedDivisors = divisors.withIndex().map { it.index to it.value }

        // Create a map to store the divisibility score for each (index, divisor) pair
        val divisorScoreMap = mutableMapOf<Pair<Int, Int>, Int>()

        // Initialize the map with default scores
        for (pair in indexedDivisors) {
            divisorScoreMap[pair] = 0
        }

        // Calculate the divisibility score for each (index, index) pair
        for (num in nums) {
            for (pair in indexedDivisors) {
                val (index, divisor) = pair
                if (num % divisor == 0) {
                    // val key = Pair(index, divisor)
                    val key = index to divisor
                    divisorScoreMap[key] = divisorScoreMap.getOrDefault(key, 0) + 1
                }
            }
        }
        
        // Find the (index, divisor) pair with the maximum score
        var maxScore = -1
        var bestDivisor = Int.MAX_VALUE
        for ((key, score) in divisorScoreMap) {
            val (index, divisor) = key
            if (score > maxScore || (score == maxScore && divisor < bestDivisor)) {
                maxScore = score
                bestDivisor = divisor
            }
        }

        return bestDivisor
    }
}

// Ways_1_2
class Solution {
    var maxScore = -1
    var bestDivisor = -1
    fun maxDivScore(nums: IntArray, divisors: IntArray): Int {
        bestDivisor = divisors[0]
        val indexedDivisors = divisors.withIndex().map { it.index to it.value }

        val divisorScoreMap = mutableMapOf<Pair<Int, Int>, Int>()
        
        // or as fllows:
        // val indexedDivisors = divisors.withIndex()
        // val divisorScoreMap = mutableMapOf<IndexedValue<Int>, Int>()

        for (num in nums) {
            for (pair in indexedDivisors) {
                val (index, divisor) = pair
                if (num % divisor == 0) {
                    divisorScoreMap[pair] = divisorScoreMap.getOrDefault(pair, 0) + 1
                }
            }
        }

        if (divisorScoreMap.isNotEmpty()) {
            for ((key, score) in divisorScoreMap) {
                val (index, divisor) = key
                compareMaxAndScore(score, divisor)
            }
        } else {
            for (pair in indexedDivisors) {
                val (index, divisor) = pair
                compareMaxAndScore(0, divisor)
            }
        }

        return bestDivisor
    }

    private fun compareMaxAndScore(score: Int, divisor: Int): Unit {
        if (score > maxScore || (score == maxScore && divisor < bestDivisor)) {
            maxScore = score
            bestDivisor = divisor
        }
    }
}

// Ways_1_3
class Solution {
    fun maxDivScore(nums: IntArray, divisors: IntArray): Int {
        val count = mutableMapOf<IndexedValue<Int>, Int>()
        var maxScore = -1
        var bestDivisor = Int.MAX_VALUE
        for (pair in divisors.withIndex()) {
            val (index, divisor) = pair
            count[pair] = 0 // 初始化，避免count这个Map为空
            for (num in nums) {
                if (num % divisor == 0) {
                    count[pair] = count.getOrDefault(pair, 0) + 1
                }
            }
        }

        for (pair in divisors.withIndex()) {
            var score = count[pair]
            score?.let {
                val (index, divisor) = pair
                if (score > maxScore || (score == maxScore && divisor < bestDivisor)) {
                    maxScore = score
                    bestDivisor = divisor
                }
            }
        }

        return bestDivisor
    }
}

// Ways_2
Class Solution {
    fun maxDivScore(nums: IntArray, divisors: IntArray): Int {
        // Initialize variables to keep track of the maximum score and the best divisor
        var maxScore = -1
        var bestDivisor = Int.MAX_VALUE
        
        // Iterate over each divisor
        for (divisor in divisors) {
            var score = 0
            // Calculate the score for the current divisor
            for (num in nums) {
                if (num % divisor == 0) {
                    score++
                }
            }
            // Update the maximum score and the best divisor if needed
            if (score > maxScore || (score == maxScore && divisor < bestDivisor)) {
                maxScore = score
                bestDivisor = divisor
            }
        }
        return bestDivisor
}
```

## Tricks
### 1
我：divisors.withIndex().map { it.index to it.value }，那么请问divisors.withIndex()返回的是啥？是一个List吗？map函数介绍一下

chatGPT4o And me:

/**

* Returns a lazy [Iterable] that wraps each element of the original array

* into an [IndexedValue] containing the index of that element and the element itself.

*/

public fun IntArray.withIndex(): Iterable<IndexedValue<Int>> {

return IndexingIterable { iterator() }

}

divisors.withIndex() 返回一个 IndexedValue 对象的序列（Sequence<IndexedValue<T>>），其中每个 IndexedValue 对象包含一个索引 (index) 和一个对应的值 (value)。这不是一个 List，而是一个惰性序列，可以用于遍历而不会立即计算所有元素。

/**

* Returns a list containing the results of applying the given [transform] function

* to each element in the original collection.

*

* @sample samples.collections.Collections.Transformations.map

*/

public inline fun <T, R> Iterable<T>.map(transform: (T) -> R): List<R> {

return mapTo(ArrayList<R>(collectionSizeOrDefault(10)), transform)

}

map 函数用于对序列的每个元素应用一个给定的转换函数，并返回转换结果的新列表。对于 Sequence<IndexedValue<T>>，map 会将每个 IndexedValue 转换成 Pair(index, value)，最终生成一个包含这些 Pair 的列表。

示例：

val divisors = intArrayOf(97, 16, 7, 60, 6, 57, 73, 84, 17, 8, 77, 60, 7, 74, 74, 24, 52, 43, 94, 48, 9, 99)

// Using withIndex() to create a sequence of IndexedValue

val indexedDivisors = divisors.withIndex()

// Converting IndexedValue to Pair(index, value) and collecting into a list

val pairsList = indexedDivisors.map { it.index to it.value }

### 2
我：val (index, divisor) = pair val key = Pair(index, divisor) val key = index to divisor 是不是解构和反解构？

chatGPT4o And Me：是的，`val (index, divisor) = pair` 和 `val key = Pair(index, divisor)` 以及 `val key = index to divisor` 涉及了解构和构造操作。让我们详细解释一下：

### 解构

解构是一种将复合数据结构（例如 `Pair` 或 `IndexedValue`）的元素拆分成单独变量的操作。Kotlin 提供了一种简洁的语法来解构这些结构：

```kotlin

val pair = Pair(1, 2)

val (first, second) = pair

// 现在 first = 1, second = 2

```

在你的例子中：

```kotlin

val pair = indexedDivisors[0] // 假设 pair 是 IndexedValue<Int> 或 Pair<Int, Int>

val (index, divisor) = pair

// 现在 index 和 divisor 分别是 pair 中的第一个和第二个元素

```

### 反解构（构造）

反解构指的是将单独的变量重新组合成复合数据结构。例如，可以使用 `Pair` 构造函数或 `to` 中缀函数来创建 `Pair`：

```kotlin

val key = Pair(index, divisor)

// 或者

val key = index to divisor

```

在你的例子中：

```kotlin

val key = Pair(index, divisor)

// 或者

val key = index to divisor

```

这两种方式都将 `index` 和 `divisor` 组合成一个 `Pair`。

### 总结

- `val (index, divisor) = pair` 是解构操作，将 `pair` 解构成 `index` 和 `divisor` 两个单独的变量。

- `val key = Pair(index, divisor)` 和 `val key = index to divisor` 是构造操作，将 `index` 和 `divisor` 组合成一个 `Pair`。

- val pairIndex = IndexedValue(index, divisor)也是构造操作，将index和divisor组合成一个IndexedValue。

两者是相反的操作，一个是将复合数据结构拆解为单独变量，另一个是将单独变量组合成复合数据结构。

我：divisors.associateWith{0}，是不是将IntArray转化成不可修改的Map？

chatGPT4o: 是的，`divisors.associateWith { 0 }` 将 `IntArray` 转换成一个不可修改的 `Map`，其中每个元素作为键，对应的值都被初始化为 `0`。这个操作会将数组中的每个元素映射到一个初始值 `0`，并返回一个不可修改的 `Map`。

### 详细解释

#### `associateWith` 函数

`associateWith` 函数的作用是根据给定的转换函数，将原始集合的每个元素作为键，并将转换函数返回的值作为对应的值，创建一个 `Map`。

例如：

```kotlin

val divisors = intArrayOf(97, 16, 7, 60, 6)

val map = divisors.associateWith { 0 }

// 结果: map = {97=0, 16=0, 7=0, 60=0, 6=0}

```

在这个例子中，`divisors.associateWith { 0 }` 创建了一个 `Map`，其中 `divisors` 数组的每个元素作为键，对应的值都是 `0`。

#### 不可修改的 Map

`associateWith` 函数返回的 `Map` 是不可修改的，即创建后无法再修改其中的键值对。

如果你需要一个可修改的 `Map`，可以将其转换为 `MutableMap`：

```kotlin

val mutableMap = divisors.associateWith { 0 }.toMutableMap()

```

### 总结

`divisors.associateWith { 0 }` 将 `IntArray` 转换为一个不可修改的 `Map`，其中每个元素作为键，对应的值都被初始化为 `0`。如果需要对 `Map` 进行修改，可以使用 `toMutableMap()` 将其转换为可修改的 `MutableMap`。【associateWith也可用于List；同时将List转换成Map也可用groupingBy加上eachCount MEMO 】

# Python

```Python
// Ways_1
class Solution:
    def maxDivScore(self, nums: List[int], divisors: List[int]) -> int:
        maxScore, bestDivisor = -1, float('inf')
        for divisor in divisors:
            score = 0
            for num in nums:
                if num % divisor == 0:
                    score += 1
            if score > maxScore or (score == maxScore and divisor < bestDivisor):
                maxScore = score
                bestDivisor = divisor
        return bestDivisor
        
// Ways_2
class Solution:
    def maxDivScore(self, nums: List[int], divisors: List[int]) -> int:
        count = Counter()
        maxScore, bestDivisor = -1, float('inf')
        for (index, divisor) in enumerate(divisors):
            for num in nums:
                if num % divisor == 0:
                    count[(index, divisor)] += 1

        for (index, divisor) in enumerate(divisors):
            score = count[(index, divisor)]
            if score > maxScore or (score == maxScore and divisor < bestDivisor):
                maxScore = score
                bestDivisor = divisor

        return bestDivisor
```
## Tricks
我：为啥在python当中的最大值不是int('inf')而是float('inf')，python当中没有double吗？

chatGPT4o: 在 Python 中，`float('inf')` 用于表示无穷大，而 `int` 类型没有对应的无穷大表示。Python 的 `float` 类型实际上是基于 C 语言中的 `double` 类型实现的，所以 `float('inf')` 实际上是双精度浮点数无穷大。

### Python 的 `float` 和 `double`

Python 中没有独立的 `double` 类型，所有的浮点数都用 `float` 类型表示。`float` 在 Python 中使用双精度（64 位）浮点数表示，与 C 语言中的 `double` 类型等效。

### 使用 `float('inf')` 的原因

由于 `int` 类型不支持无穷大，使用 `float('inf')` 是表示正无穷大的唯一方法。它在算法中通常用于初始化比较中的最小值或最大值，以确保任何实际数值都能替换它。

### 示例代码

下面是一个示例，展示了如何在 Python 中使用 `float('inf')`：

```python

# 初始化一个变量为正无穷大

max_value = float('inf')

# 初始化一个变量为负无穷大

min_value = -float('inf')

# 打印这些值

print("Positive infinity:", max_value)

print("Negative infinity:", min_value)

# 比较时，这些值会按预期工作

print("Is 1000 less than infinity?", 1000 < max_value)

print("Is -1000 greater than negative infinity?", -1000 > min_value)

```

输出：

```

Positive infinity: inf

Negative infinity: -inf

Is 1000 less than infinity? True

Is -1000 greater than negative infinity? True

```

### 总结

- Python 中的 `float` 类型相当于 C 语言中的 `double` 类型。

- 使用 `float('inf')` 来表示正无穷大，因为 `int` 类型没有无穷大的表示方法。

- `float` 类型在 Python 中使用双精度浮点数表示，因此没有单独的 `double` 类型。