```
const formatCdn = (source, width, height) => {
  if (!source) {
    return 'http://images.idf66.com/2017/09/17/fa44f8cb-6548-4092-b35e-1c12d23113dc.png'
  }
  const sourceSplit = source.split('.')
  if (source.indexOf('//') === 0) {
    source = `https:${source}`
  } else if (source.split('//').length > 1) {
    source = `https://${source.split('//')[1]}`
  }
  if (source) source = source.replace(/szdf\.oss-cn-shenzhen\.aliyuncs\.com/, 'images.idf66.com')
  source += '?x-oss-process=image/resize,m_fixed,'
  const heightType = typeof height
  if (heightType === 'object') {
    source += `h_${height},w_${width}`
  } else if (height === '0') {
    source += `w_${width}`
  } else if (width === '0') {
    source += `h_${width}`
  } else {
    source += `h_${height},w_${width}`
  }
  if (sourceSplit[sourceSplit.length - 1] === 'png') {
    source += '/format,png'
  }
  return source
}
```