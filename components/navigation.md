react-navigation组件用于页面导航
使用方法
- router.js
    ```
    // 引入组件用于导航
    import UserCenter from './pages/user/center/connect'
    // 定义导航名称
    Me: { screen: UserCenter },
    ```
- headher.js
    ```
    import React from 'react'
    import { View } from 'react-native'
    import { NavBar } from 'app/components/ui'

    export default class Header extends React.Component {
    onLeftClick() {
        const { onGoBack } = this.props
        if (onGoBack) {
        onGoBack(this.props.navigation.goBack)
        } else {
        this.props.navigation.goBack()
        }
    }
    render() {
        const { getScreenDetails, scene } = this.props
        const headerTitle = getScreenDetails(scene).options.headerTitle
        const rightBottom = getScreenDetails(scene).options.rightBottom
        return (
        <View>
            <NavBar onLeftClick={this.onLeftClick.bind(this)} rightContent={rightBottom} >
            {headerTitle}
            </NavBar>
        </View>
        )
    }
    }
    ```
- 使用
    ```
    // 跳转路由的时候可以传递参数
    this.props.navigation.navigate('userCenter', {params})
    ```
> 注意：react-native-navigation的goback()函数并不会刷新返回的页面，也没有提供reload方法。
  如果想要刷新可以使用
  ```
  // pageA
  this.listener = DeviceEventEmitter.addListener('refresh', () => { this.refresh() })
  // pageB
  DeviceEventEmitter.emit('refresh')
  ```
  这样有A->B再有B->A，pageA就可以执行自身的refresh函数了。

> but上面的方法不支持web，但是值得注意的是B页面的props或者state更新的时候，A页面的WillUpdate、DidUpdate
   会执行，因此也可以通过这种方式满足部分需求