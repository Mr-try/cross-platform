- 在进行<View>嵌套布局时，除了内部的View标签指定高度，最外层的标签也要指定minHeight/height不然在
  部分机型(华为P8、P9)和部分浏览器(iphone 5s ios9.3及其一下)会出现高度塌陷等问题
- 不要使用lineheight，同时<Text>标签的fontSize最好不要与height相等，在部分机型中font底部会缺失。
- borderWidth最好不要出现小数情况，在iPhone6p、7p上会显得格外粗和其他意想不到的情况
- 使用webView嵌套图片的时候除了width:100%同时也要指定height:auto，不然会出现图片拉伸现象
- ScrollView如果直接嵌套一个ScrollView(ListView)，在部分机型下的web会导致高度塌陷问题
  因为react-native-web之前把其中一层的flex-basis设成了0%，升级到0.0.130就行了。