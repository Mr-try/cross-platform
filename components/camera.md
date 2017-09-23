扫一扫功能需要用到摄像头功能，选取[react-native-camera](https://github.com/lwansbrough/react-native-camera)
> 功能描述：
    扫码跳转到详情页，IOS要对没有权限访问做提示处理，10s内没有扫到内容也要做处理

使用方法
```
const Camera = require('react-native-camera').default
export default class BadInstagramCloneApp extends React.Component {
  static navigationOptions = {
    headerTitle: '扫一扫',
    header: headerProps => <Header {...headerProps} />,
  }
  constructor(props) {
    super(props)
    this.state = {
      TorchMode: false,
      modal: false,
      scannothing: false,
      fadeAnim: new Animated.Value(115),
      getResult: false,
      cameraAvailable: true,
    }
    this.animating = true
  }
  async componentWillMount() {
    if (isIOS) {
      const isAuthorized = await Camera.checkVideoAuthorizationStatus()
      if (isAuthorized) {
        this.setState({ cameraAvailable: true, modal: false }, () => { this.timer = setTimeout(() => this.props.navigation.navigate('ScanList', { itemCode: '' }), 500) })
      } else {
        this.setState({ cameraAvailable: false, modal: true })
      }
    } else {
      await PermissionsAndroid.request(PermissionsAndroid.PERMISSIONS.CAMERA)
      const granted = await PermissionsAndroid.check(PermissionsAndroid.PERMISSIONS.CAMERA)
      if (granted) {
        this.setState({ cameraAvailable: true, modal: false }, () => { this.timer = setTimeout(() => this.props.navigation.navigate('ScanList', { itemCode: '' }), 50000) })
      } else {
        this.setState({ cameraAvailable: false, modal: true })
      }
    }
  }
  componentDidMount() {
    console.log('didmount', 1212)
  }
  componentWillUnmount() {
    // 卸载定时器
    this.timer && clearTimeout(this.timer)
    this.animating = false
  }
  onClose() {
    this.props.navigation.goBack()
  }
  click(path) {
    this.props.navigation.navigate(path)
  }
  switchMode() {
    if (this.state.TorchMode === true) {
      this.setState({ TorchMode: false })
    } else {
      this.setState({ TorchMode: true })
    }
  }
  doRouterScanDetail(result) {
    this.setState({ scannothing: false })
    const itemCode = result.data
    this.props.navigation.navigate('ScanList', { itemCode })
  }
  renderAnimate() {
    this.state.fadeAnim.setValue(115)
    Animated.timing(
      this.state.fadeAnim,
      {
        duration: 1000,
        toValue: 391,
      }
    ).start(() => { this.animating && this.renderAnimate() })
    return (
      <Animated.View
        style={{
          ...styles.scanline,
          ...this.props.style,
          top: this.state.fadeAnim,
        }}
      >
      </Animated.View>
    )
  }
  renderTimer() {
  }
  renderBody() {
    // this.renderTimer()
    const codeType = isAndroid ? ['qr', 'codabar', 'code128', 'code39', 'ean13', 'ean8', 'upc', 'msi', 'code93'] : ['org.iso.Code128', 'org.iso.Code39', 'org.iso.Code39Mod43', 'com.intermec.Code93', 'org.iso.QRCode', 'org.gs1.EAN-13', 'org.gs1.EAN-8', 'com.intermec.Code93']
    return (
      <Camera style={{ flex: 1 }}
        barCodeTypes={codeType}
        torchMode={this.state.TorchMode ? Camera.constants.TorchMode.on : Camera.constants.TorchMode.off}
        onBarCodeRead={(result) => {
          if (!this.didPop) {
            this.didPop = true
            this.doRouterScanDetail(result)
          }
        }}
      >
        <View style={styles.scanWrap}>
        </View>
        {/* {this.renderAnimate()} */}
        {/* <TouchableHighlight style={styles.pictureWarp} onPress={this.openPicture.bind(this)} underlayColor='transparent'>
          <View style={styles.picture}>
            <Svg type='picture' width='44' height='44' />
          </View>
        </TouchableHighlight> */}
        <View style={styles.leftTop}></View>
        <View style={styles.rightTop}></View>
        <View style={styles.leftBottom}></View>
        <View style={styles.rightBottom}></View>
        <View style={[styles.commonOpacity, styles.left]}></View>
        <View style={[styles.commonOpacity, styles.right]}></View>
        <View style={[styles.commonOpacity, styles.top]}></View>
        <View style={[styles.commonOpacity, styles.bottom]}></View>
        <View style={[styles.commonOpacity, styles.noticeWrap]}>
          <Text style={styles.notice}>将条码/二维码放入框内</Text>
        </View>
        <TouchableHighlight style={styles.lighterWarp} onPress={this.switchMode.bind(this)} >
          <View style={styles.lighter}>
            <Svg type={this.state.TorchMode ? 'lighteron' : 'lighteroff'} height='64' width='42.6' />
          </View>
        </TouchableHighlight>
      </Camera>
    )
  }
  renderNoAuth() {
    return (
      <View>
        <Modal
          transparent
          maskClosable={false}
          visible
          footer={[{ text: '确定', onPress: () => { this.onClose() } }]}
        >
          <View style={styles.authNoticeWrap}>
            <Text>该程序没有权限访问摄像功能</Text>
            {
              isIOS ? <Text>请在设置->隐私->相机中开启</Text>
                : <Text>请在设置->应用程序->i免税->权限中开启</Text>
            }
          </View>
        </Modal>
      </View>
    )
  }
  renderNoresult() {
    return (
      <View>
        <Image source={require('app/images/scan-nothing.png')} style={styles.topBgImgStyle} resizeMode={'stretch'} />
        <Text style={styles.sorryText}>抱歉，没有查询到此商品</Text>
        <View style={styles.buttonViewStyle}>
          <Button size='large' type='primary' onClick={this.click.bind(this, 'Home')}>返回首页</Button>
        </View>
      </View>
    )
  }
  render() {
    if (this.state.scannothing) {
      this.renderNoresult()
    }
    if (this.state.cameraAvailable) {
      return (
        this.renderBody()
      )
    }
    return (
      this.renderNoAuth()
    )
  }
}
```
> 关于相机权限验证camera针对android有自带的方法（Camera.checkVideoAuthorizationStatus()），而android sdk22及其一下的只要在
  AndroidManifest.xml中配置相关权限即可，对于sdk22需同以上的代码中所示的请求，两种请求都是异步的。