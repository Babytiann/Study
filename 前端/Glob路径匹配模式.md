# 一、**Glob **

### **Glob 是什么？**

Glob 是一种文件路径匹配模式，广泛应用于文件系统操作。它允许使用特殊的通配符规则查找匹配的文件或目录。例如：

- `*` 表示匹配任意多个字符（不包括 `/`）。
- `**` 表示匹配任意深度的文件夹。
- `{}` 表示多个选项，比如 `{a,b}` 会匹配 `a` 和 `b`。
- `?` 表示匹配单个字符。

在 `import.meta.glob` 中，这种模式用于指定文件的匹配规则，比如：

- `./dir/*.js`：匹配 `dir` 目录下所有的 `.js` 文件。
- `./dir/**/*.js`：匹配 `dir` 目录下任意子目录及文件的 `.js` 文件。

------

### **使用场景**

1. **批量导入组件** 适合在 Vue 或 React 项目中，根据文件名自动注册组件。

   ```
   const components = import.meta.glob('./components/*.vue');
   for (const path in components) {
     components[path]().then((mod) => {
       console.log(`Loaded component: ${path}`, mod.default);
     });
   }
   ```

2. **国际化文件** 动态加载不同语言的 JSON 文件，实现国际化支持。

   ```
   const locales = import.meta.glob('./locales/*.json');
   const loadLocale = async (lang) => {
     const module = await locales[`./locales/${lang}.json`]();
     return module.default;
   };
   
   loadLocale('en').then((data) => console.log(data));
   ```

### **优势**

1. **高效的代码分割**：只加载需要的模块，而非所有文件。
2. **无需手动维护路径**：通过文件匹配模式自动管理。
3. **动态加载**：支持按需加载，提高应用性能。

