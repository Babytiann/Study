# 一.Vue-router对比React-router

​	React Router 和 Vue Router 的路由策略确实有不同的行为，尤其在如何渲染嵌套路由时有所差异。

## 1.React Router 的行为

在 React Router 中，嵌套路由是层叠渲染的。也就是说，父路由的组件和子路由的组件会同时渲染。具体来说：

- 在访问 `/dashboard/settings` 时，`Dashboard` 组件会渲染，然后在 `Dashboard` 组件的 `children` 区域再渲染 `Settings` 组件。
- 也就是说，`Dashboard` 会作为父组件存在，`Settings` 会作为它的子组件展示在合适的位置。

这种方式可以让你在父组件中控制布局和共享状态，同时在子路由中渲染不同的内容。

## 2.Vue Router 的行为

​	Vue Router 默认的行为是**只渲染匹配到的路由组件**。在 Vue Router 中，父路由的组件并不会自动渲染。只有当你使用了 `<router-view>` 来标记子路由的渲染位置时，子组件才会渲染。

- 在访问 `/dashboard/settings` 时，Vue Router 会直接渲染 `Settings` 组件，而不会自动渲染 `Dashboard`，除非你在 `Dashboard` 路由组件中使用了 `<router-view>` 来展示它的子路由。