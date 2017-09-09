>  antd mobiel https://mobile.ant.design/docs/react/introduce

> antd mobile组件算是目前来说三端统一可选的最佳途径，但是因为其不稳定性且BUG太多，
  所以引用antd的时候需要做一层处理
例：
在引用antd tabs组件的时候，因为antd的tabs在三端上表现不是一致，比如说在android上开启动画效果
    会导致tabs切换不起效果,因此，
1. 对于RN要做如下处理(tabs/index.js)
    
``` 
        import { Tabs } from 'antd-mobile'
        import React, { Component } from 'react'        
        import variables from 'themes/default'
        import { Platform } from 'react-native'

        class PureTabs extends Component {
        render() {
        const customProps = Object.assign({
            activeTextColor: variables.tabs_color,
            activeUnderlineColor: variables.tabs_color,
            animated: Platform.OS !== 'android',
            }, this.props)
                return (
                <Tabs {...customProps}>
                    { customProps.children }
                </Tabs>
                )
        }
        }
        PureTabs.TabPane = Tabs.TabPane
        export default PureTabs
```
2. 而web则不用做处理，只需要将antd的tabs引入再export出去就行(tabs/index.web.js)
``` 
    import { Tabs } from 'antd-mobile'
    import './index.less'
    export default Tabs
```
3. 在使用webpack打包的时候,web端优先寻找扩展名为index.web.**的文件
```
        resolve: {
        alias: {
            color: path.join(__dirname, './themes/default.less'),
            'react-native': 'react-native-web',
            'react-navigation': 'react-navigation/lib/react-navigation.js',
            app: path.join(__dirname, './app'),
            themes: path.join(__dirname, './themes'),
            settings: path.join(__dirname, './settings'),
        },
        modules: [
            path.resolve(__dirname, 'web_modules/node_modules'),
            'node_modules',
        ],
        extensions: ['.web.tsx', '.tsx', '.web.js', '.js', '.less', '.web.jsx', '.jsx', '.json'],
```
4. 在开发的时候也有三种打开项目的命令行(package.json)

```
        "scripts": {
            "start": "node node_modules/react-native/local-cli/cli.js start",
            "web": "export NODE_ENV=local && webpack --progress --color -w",
            "development": "export NODE_ENV=development && webpack --progress --color -p",
            "build": "export NODE_ENV=prepub && webpack --progress --color -p",
            "ios": "react-native run-ios",
            "android": "react-native run-android"
        },
```
    其中npm run web/ios/android对应三端
> 目的将antd的组件从我们自己的系统做一次处理。