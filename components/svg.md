由于项目需要用到彩色图标，而原有的iconfont不支持，因而采用svg。
步骤：
- 定义Svg图标对象数组
    ```
    export default {
        delete: [
            {d: '....',fill: '#999999',},
            {d: '....',fill: '#999999',}        
        ],
        edit: [
            {d: '....',fill: '#999999',}
        ],
    ```
- web原生支持svg，不做特殊处理
- rn需要引入react-native-svg
- 编写web svg通用组件（web svg）
    ```
    <svg
        width={(width || ICON_SIZE[size] || 22) * PixelRatio.get()}
        height={(height || ICON_SIZE[size] || 22) * PixelRatio.get()}
        viewBox={Icons.viewBox[type] || '0 0 1024 1024'}
        style={{
          ...style,
        }}
      >
        {
          Icons[type].map((path, i) => {
            let fill = path.fill
            if (newfill) {
              fill = path.fill === replacedfill ? newfill : path.fill
            }
            return <path key={i} d={path.d} fill={fill} />
          })
        }
      </svg>
    ```
- 编写rn Svg通用组件（注意标签名）
    ```
     <Svg
        width={width || ICON_SIZE[size] || 22}
        height={height || ICON_SIZE[size] || 22}
        viewBox={Icons.viewBox[type] || '0 0 1024 1024'}
        style={{
          ...style,
        }}
      >
        {
          Icons[type].map((path, i) => {
            let fill = path.fill
            if (newfill) {
              fill = path.fill === replacedfill ? newfill : path.fill
            }
            return <Path key={i} d={path.d} fill={fill} />
          })
        }
      </Svg>
      ```
- 两个组件对外暴露的接口完全一致
- 使用
    ```
     <Svg 
        style={style.operationIcon} 
        newfill='#fff' 
        replacedfill='#FFCD30' 
        type='edit' 
        size='md'
    />
    ```
> 说明，newfill、replacedfill是为了满足当前业务需要临时解决方案，最好的解决方案是
  newfill=[]、replacedfill=[]，替换的颜色一一对应，达到可替换图标颜色目的。如果固定不变
  则可删除替换颜色的接口