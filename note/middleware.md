> 为了应对某些特定的应用场景，需要通过(herd)中间件来实现(herd:对koa的一种再封装)
  比如，在判断有没有未读消息的时候需要判断是否登录，因为未登录状态为401，所以需要在
  中间件层，将401状态的未读消息接口的返回内容改为0.
```
  router.get('/api/news/number', async (ctx) => {
    if (ctx.herdContext._USER_) {
      const result = await ctx.invokeService('newsUnreadNumber')
      ctx.body = result
      return
    }
    ctx.body = 0
  })
```