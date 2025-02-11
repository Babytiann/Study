# 1.get和set

​	**getter** (`get`)：用于在读取属性时定义返回的值。你可以在 `get hello()` 中定义读取属性 `hello` 时需要执行的逻辑。比如，你可以在读取 `hello` 时对值进行处理或转换。

​	**setter** (`set`)：用于在赋值属性时定义赋值时的行为。你可以在 `set hello(value)` 中定义当给属性 `hello` 赋值时的操作。例如，你可以对输入的 `value` 做校验或格式化操作。

计算属性也是这样，比如有下面一个计算属性

```
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // 注意：我们这里使用的是解构赋值语法
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

```
console.log(fullName.value);  // 调用 get 方法，输出 "John Doe"

fullName.value = 'Jane Smith';  // 调用 set 方法，将 firstName 和 lastName 更新为 'Jane' 和 'Smith'
console.log(firstName.value);  // 输出 "Jane"
console.log(lastName.value);   // 输出 "Smith"
```

