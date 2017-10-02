> react-native-webView组件用于页面显示h5页面，一般是用来显示后台配置的文章

index.js
```
···
import { WebView, View } from 'react-native'
class PureWebView extends Component {
  constructor(props) {
    super(props)
    this.state = {
      WebViewHeight: 0,
    }
  }
  render() {
    const { Html } = this.props
    return (
      <View style={{ height: this.state.WebViewHeight }}>
        <WebView
          source={{ html: Html }}
          style={{ flex: 1 }}
          bounces={false}
          scrollEnabled={false}
          automaticallyAdjustContentInsets
          scalesPageToFit={false}
          contentInset={{ top: 0, left: 0, bottom: 0, right: 0 }}
          onNavigationStateChange={({ title }) => {
            if (title) {
              this.setState({
                WebViewHeight: Number(title) + 20,
              })
              console.log(Number(title))
            }
          }}
        >
        </WebView>
      </View>
    )
  }
}
···
```
index.web.js
```
···
import { View } from 'react-native'
class PureWebView extends Component {
  render() {
    const { Html } = this.props
    return (
      <View style={{ flex: 1 }}>
        <View dangerouslySetInnerHTML={{ __html: Html }}></View>
      </View>
    )
  }
}
···
```
> 使用

```
import { Tabs, WebView, Popup } from 'app/components/ui'

  renderPage() {
    const { content } = this.props.articleDetail
    const Html = content ? content.toString() : null
    const HTML = `<!DOCTYPE html>
    <html>
    <body>
    ${Html}
    <script>
            var img = document.getElementsByTagName('img');
            for(var i=0;i<img.length;i++){
             img[i].style.width='100%';
            }
            window.onload=function(){
            document.title = document.body.clientHeight;
            window.location.hash = 1;
            }
    </script>
    </body></html>`
    return (
      <WebView Html={HTML}></WebView>
    )
  }
  ```