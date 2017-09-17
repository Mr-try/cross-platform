> 进行rn表单验证时候需要引入rc-form

如下：
```
<ControlledTextInput
        {...getFieldProps('mobile', {
          rules: [{ required: true }],
          onChange: val => this.setState({ mobile: val }),
        })}
        style={style.inputTextStyle}
        underlineColorAndroid='transparent'
        placeholder='请输入手机号'
        maxLength={11}
        clearButtonMode={'while-editing'}
        >
</ControlledTextInput>
```
如果使用原生的TextInput或者antd的InputItem则会warning该组件是受控组件，onChange事件必须放在标签根目录，
因此需要对TextInput或者InputItem做一次封装处理：
ControlledTextInput
```
import React from 'react'
import { TextInput } from 'react-native'
import Styles from './style'
import variables from 'themes/default'

const fixControlledValue = (value) => {
  if (typeof value === 'undefined' || value === null) {
    return ''
  }
  return value
}

class ControlledTextInput extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      focused: props.focused || false,
    }
  }

  componentDidMount() {
    if (this.props.autoFocus || this.props.focused) {
      this.input.focus()
    }
  }
  componentWillReceiveProps(nextProps) {
    if (nextProps.focused !== this.state.focused) {
      this.setState({
        focused: nextProps.focused,
      })
    }
  }
  componentDidUpdate() {
    if (this.props.focused) {
      this.input.focus()
    }
  }

  handleChange = (e) => {
    const value = e.nativeEvent ? e.nativeEvent.text : e.target.value
    this.props.onChange(value)
  }

  render() {
    const { value, style, focused, ...otherProps } = this.props
    const fixedValue = fixControlledValue(value)
    return (
      <TextInput
        ref={(input) => { this.input = input }}
        placeholderTextColor={variables.color_text_secondary}
        {...otherProps}
        underlineColorAndroid='transparent'
        style={[Styles.textInput, style]}
        onChange={this.handleChange}
        value={fixedValue}
      />
    )
  }
}

export default ControlledTextInput
```
提交表单部分
```
  onSubmit() {
    this.props.form.validateFields((error) => {
      if (error) return this.tipFail(error)
      const registerInfo = this.props.form.getFieldsValue()
      registerInfo.channel = getChannel()
      return this.props.register(serializeData(registerInfo), this.jumpToNext.bind(this))
    })
  }
  tipFail(error) {
    let message = ''
    if (error.mobile) {
      message = '请输入手机号'
    } else if (error.captcha) {
      message = '请输入图片验证码'
    } else if (error.code) {
      message = '请输入短信验证码'
    } else if (error.password) {
      message = '请输入密码'
    }
    Toast.info(message, 2, () => {}, false)
  }
```
> 如果大量用到tipFail，则可对提示进行专门的封装处理