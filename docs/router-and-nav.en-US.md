---
order: 3
title: Router and Nav
type: Development
---

Routing and menus are the key skeletons for organizing an application. The routes in pro are centrally managed in a convenient way to manage and manage them in [`config.ts`](https://github.com/ant-design/ant-design-pro/blob/33f562974d1c72e077652223bd816a57933fe242/config/config.ts).

## Basic Structure

In this part, scaffolding builds the basic framework of routing and menus by combining some configuration files, basic algorithms and tool functions, mainly involving the following modules/functions:

- `Routing Management` Configure the route in [`config.ts`](https://github.com/ant-design/ant-design-pro/blob/33f562974d1c72e077652223bd816a57933fe242/config/config.ts) according to the agreed syntax.
- `Menu generation` Generates a menu based on the routing configuration. The name of the menu item, the nested path is highly coupled to the route.
- Breadcrumbs component The breadcrumbs built into [PageHeader](http://v2-pro.ant.design/components/PageHeader) can also be automatically generated from the configuration information provided by the scaffolding.

The following is a brief introduction to the basic ideas of each module. If you are not interested in the implementation process, just want to know how to implement the relevant requirements, you can directly view [requirements instance](/docs/router-and-nav#Example).

### Router

At present, all the routes in the scaffolding are managed by [`config.ts`](https://github.com/ant-design/ant-design-pro/blob/33f562974d1c72e077652223bd816a57933fe242/config/config.ts). In the configuration of umi, we add some parameters, such as `name`, `icon`, `hideChildren`, `authority`, to assist the generation. menu. among them:

- `name` and `icon` represent the icon and text of the generated menu item, respectively.
- `hideChildrenInMenu` is used to hide sub-routes that do not need to be displayed in the menu. Usage can view the configuration of the `Step by Step Form`.
- `hideInMenu` can not display this route in the menu, including sub-routing. The effect can be viewed on the `exception/trigger` page.
- `authority` is used to configure the permissions of this route. If configured, it will verify the permissions of the current user and decide whether to display it.
  > You may notice that the `name` in the configuration is different from the actual display of the menu. This is because we use the global component, see [i18n](/docs/i18n/).

### Menu

The menu is generated according to [`config.ts`](https://github.com/ant-design/ant-design-pro/blob/33f562974d1c72e077652223bd816a57933fe242/config/config.ts).

> If your project does not require a menu, you can do it at [src/layouts/BasicLayout.tsx](https://github.com/ant-design/ant-design-pro/blob/master/src/layouts/BasicLayout.tsx#L116) by setting `menuRender={false}`.

### Fetch menu from server

Just update `menuData` in [models/menu](https://github.com/ant-design/ant-design-pro/blob/master/src/models/menu.js#L111), which is a json array. Just the server returns a json of similar format.

```js
[
  {
    path: '/dashboard'，
    name: 'dashboard'，
    icon: 'dashboard'，
    children: [
      {
        path: '/dashboard/analysis'，
        name: 'analysis'，
        exact: true，
      }，
      {
        path: '/dashboard/monitor'，
        name: 'monitor'，
        exact: true，
      }，
      {
        path: '/dashboard/workplace'，
        name: 'workplace'，
        exact: true，
      }，
    ]，
  }
  ...
]
```

> Note that path must be defined in config.ts.(All you need in Conventional Routing is the correct page.)

### Bread Crumbs

Breadcrumbs are implemented by `PageHeaderWrapper`, `MenuContext` will be passed to `PageHeader` via props according to the `breadcrumbNameMap` generated by `MenuData`. If you want to make custom breadcrumbs, you can modify the incoming `breadcrumbNameMap` solve.

`breadcrumbNameMap` sample data is as follows:

```js
{
  '/': { path: '/', redirect: '/dashboard/analysis', locale: 'menu' },
  '/dashboard/analysis': {
    name: 'analysis',
    component: './Dashboard/Analysis',
    locale: 'menu.dashboard.analysis',
  },
  ...
}
```

## Example

The above outlines the implementation of this part, and then through the actual case to explain what to do.

### Menu jump to a URL

You can fill the url directly into the path and the framework will handle it automatically.

```js
{
    path: 'https://pro.ant.design/docs/getting-started-cn',
    name: "文档"
}
```

### Add Page

Scaffolding provides two layout templates by default: `Basic Layout - BasicLayout` and `Account Layout - UserLayout`:

<img alt="BasicLayout" src="https://gw.alipayobjects.com/zos/rmsportal/oXmyfmffJVvdbmDoGvuF.png" />

<img alt="UserLayout" src="https://gw.alipayobjects.com/zos/rmsportal/mXsydBXvLqBVEZLMssEy.png" />

If your page can take advantage of both layouts, you only need to add one to the corresponding routing configuration:

```js
  // app
  {
    path: '/',
    component: '../layouts/BasicLayout',
    routes: [
      // dashboard
      { path: '/', redirect: '/dashboard/analysis' },
      { path :'/dashboard/test',component:"./Dashboard/Test"},
    ...
},
```

When added, the relevant routing and navigation will be automatically generated.

### Add layout

In the scaffolding we implement the layout template by nesting the route. [`config.ts`](https://github.com/ant-design/ant-design-pro/blob/33f562974d1c72e077652223bd816a57933fe242/config/config.ts) is an array, the first level of which is our layout. If you need to add a new layout, you can directly add a new first-level element in the array.

```js
module.exports = [
   // user
   {
    path: '/user',
    component: '../layouts/UserLayout',
    routes:[...]
   },
   // app
   {
    path: '/',
    component: '../layouts/BasicLayout',
    routes:[...]
   },
   // new
   {
    path: '/new',
    component: '../layouts/new_page',
    routes:[...]
   },
]

```

### Use a custom icon in the menu

Due to umi's limitations, the [`config.ts`](https://github.com/ant-design/ant-design-pro/blob/33f562974d1c72e077652223bd816a57933fe242/config/config.ts) is not directly With components, Pro temporarily supports the use of [`ant.design`](https://ant.design/components/icon-cn/) its own icon type, and the url of an img. Just configure it directly on the icon property. If it's a url, Pro will automatically process it as an img tag.

If this does not meet the requirements, you can customize [`getIcon`](https://github.com/ant-design/ant-design-pro/blob/master/src/components/SiderMenu/BaseMenu.js#L18) method.

> If you need to use the iconfont icon, you can try the custom icon for [ant.desgin](https://ant.design/components/icon-cn/#%E8%87%AA%E5%AE%9A%E4%B9%).

### Routing with parameters

Scaffolding supports routing with parameters by default, but it is not a good idea to display a route with parameters in the menu. We will not automatically inject a parameter for you, you may need to handle it yourself in the code.

```js
{ path: '/dashboard/:page', hideInMenu: true, name: 'analysis', component: './Dashboard/Analysis' },
```

You can jump to this route with the following code:

```js
import router from 'umi/router';

router.push('/dashboard/anyParams');

//or

import Link from 'umi/link';

<Link to="/dashboard/anyParams">go</Link>;
```

In the routing component, routing parameters can be obtained via `this.props.match.params`.

See more details:[umi#router](https://umijs.org/guide/router.html#%E7%BA%A6%E5%AE%9A%E5%BC%8F%E8%B7%AF%E7%94%B1)
