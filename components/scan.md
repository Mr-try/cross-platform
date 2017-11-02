> 扫一扫是用基于react-native-camera库来实现，不支持web，本项目也是在web上隐藏扫一扫功能
- 权限检测(因为android的权限检测比较复杂，故依次为例)
```
async function hasCameraAuth() {
  if (Platform.OS === 'ios') {
    return true
  }
  if (Platform.OS === 'android') {
    try {
      const granted = await PermissionsAndroid.request(PermissionsAndroid.PERMISSIONS.CAMERA)
      if (granted === PermissionsAndroid.RESULTS.GRANTED) {
        return true
      }
      Toast.info('请在设置中开启相机权限')
      return false
    } catch (err) {
      console.warn(err)
    }
  }
}
```
- 调用
```
 hasCameraAuth().then((hasAuth) => {
        if (hasAuth) {
          this.props.navigation.navigate(Path, params)
        }
      })
```
- 相机页面
```
  render() {
    const codeType = isAndroid ? ['qr', 'codabar', 'code128', 'code39', 'ean13', 'ean8', 'upc', 'msi', 'code93'] : ['org.iso.Code128', 'org.iso.Code39', 'org.iso.Code39Mod43', 'com.intermec.Code93', 'org.iso.QRCode', 'org.gs1.EAN-13', 'org.gs1.EAN-8', 'com.intermec.Code93']
    return (
      <View style={{ flex: 1, width: '100%', backgroundColor: '#000' }}>
        <Modal
          transparent
          maskClosable={false}
          visible={this.state.modal}
          footer={[{ text: '确定', onPress: () => { this.onClose() } }]}
        >
          <View style={styles.authNoticeWrap}>
            <Text>该程序没有权限访问摄像功能</Text>
            <Text>请在设置中开启</Text>
          </View>
        </Modal>
        <Camera
          style={{ flex: 1 }}
          aspect={Camera.constants.Aspect.fill}
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
      </View>
    )
  }
```