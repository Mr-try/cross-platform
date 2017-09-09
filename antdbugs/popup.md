- 传参问题
``` 
Popup.show(<AddressPopup 
                returnCityInfo={this.getCityInfo.bind(this)} 
                cityData={this.state.cityList} 
            />
      , { animationType: 'slide-up', maskClosable: true, onMaskClose: () => {} })
```
> 注意，虽然官方文档指出maskClosable默认值本来true，且maskClosable为true时，点击蒙层会自动关闭Popup
      但是一旦对Popup.show()传参，则其组件内部的参数全会被覆盖，因此需要重新指定那些默认的参数。然饿，web正常。
- 自身状态问题
> Popup作为一个弹出层并没有自身的状态，因此对其调用生命周期钩子函数是无效的
- 当Popup使用包裹组件的数据源时，即便包裹组件的数据源改变，Popup的数据也不会变，重新Popup.show()才能看的变化后的数据
> 因此要把修改数据的逻辑放在Popup内部处理才行，比如淘宝收货地址的所在地区选择(猜的，反正我是这么搞的)。