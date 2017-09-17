扫一扫功能需要用到摄像头功能，选取[react-native-camera](https://github.com/lwansbrough/react-native-camera)
> 功能描述：
    扫码跳转到详情页，IOS要对没有权限访问做提示处理，10s内没有扫到内容也要做处理
使用方法
```
import React from 'react'
import {
  View,
  Text,
  Image,
  TouchableHighlight
} from 'react-native'
import { isAndroid, isIOS } from 'app/utils/platform'
import Header from 'app/components/header'
import { Svg, Modal, Button } from 'app/components/ui'
import styles from './style'

const Camera = require('react-native-camera').default

export default class BadInstagramCloneApp extends React.Component {
  static navigationOptions = {
    headerTitle: '扫一扫',
    header: headerProps => <Header {...headerProps} />,
  }
  constructor(props) {
    super(props)
    this.state = {
      TorchMode: true,
      modal: false,
      scannothing: false,
    }
  }
  async componentDidMount() {
    if (isIOS) {
      const isAuthorized = await Camera.checkVideoAuthorizationStatus()
      if (isAuthorized) {
        this.setState({ cameraAvailable: true })
        this.setState({ modal: false })
      } else {
        this.setState({ cameraAvailable: false })
        this.setState({ modal: true })
      }
    } else {
      this.setState({ cameraAvailable: true })
      this.setState({ modal: false })
    }
  }
  onClose() {
    this.props.navigation.goBack()
  }
  gohome() {
    this.props.navigation.navigate('Home')
  }
  switchFlash() {
    if (this.state.TorchMode === true) {
      this.setState({ TorchMode: false })
    } else {
      this.setState({ TorchMode: true })
    }
  }
  doRouterScanDetail(result) {
    this.setState({ scannothing: false })
    const itemCode = result.data
    this.props.navigation.navigate('Item', { itemCode })
  }
  renderBody() {
    const codeType = isAndroid ? ['qr', 'codabar', 'code128', 'code39', 'ean13', 'ean8', 'upc', 'msi', 'code93'] : ['org.iso.Code128', 'org.iso.Code39', 'org.iso.Code39Mod43', 'com.intermec.Code93', 'org.iso.QRCode', 'org.gs1.EAN-13', 'org.gs1.EAN-8', 'com.intermec.Code93']
    return (
      <View style={styles.container}>
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
        </Camera>
      </View>
    )
  }
  render() {
    if (this.state.scannothing) {
      return (
        <View>
          <Image source={require('app/images/scan-nothing.png')} style={styles.topBgImgStyle} resizeMode={'stretch'} />
          <Text style={styles.sorryText}>抱歉，没有查询到此商品</Text>
          <View style={styles.buttonViewStyle}>
            <Button size='large' type='primary' onClick={this.gohome.bind(this)}>返回首页</Button>
          </View>
        </View>
      )
    }
    if (this.state.cameraAvailable) {
      setTimeout(() => {
        this.setState({ scannothing: true })
      }, 10000)
      return (
        <View style={styles.container}>
          {this.renderBody()}
          <View style={styles.scanWrap}>
          </View>
          <View style={styles.noticeWrap}>
            <Text style={styles.notice}>将条码/二维码放入框内</Text>
          </View>
          <TouchableHighlight onPress={this.switchFlash.bind(this)} underlayColor='transparent'>
            <View style={styles.lighter}>
              <Svg type='lighter' size='md'/>
            </View>
          </TouchableHighlight>
        </View>
      )
    }
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
            <Text>请在设置->隐私->相机中开启</Text>
          </View>
        </Modal>
      </View>
    )
  }
}
```