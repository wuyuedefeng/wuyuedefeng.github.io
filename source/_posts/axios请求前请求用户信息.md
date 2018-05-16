title: vue axios请求前请求用户信息
author: wsen
tags: []
categories:
  - vue
date: 2019-02-28 18:00:00
---
```
const cusAxios = axios.create({
  baseURL: process.env.API_HOST,
  timeout: 15000
})
// cusAxios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
// Add a request interceptor
let userInfoPromise = null
cusAxios.interceptors.request.use((config) => {
  console.log('begin api:', config.url)
  return new Promise((resolve) => {
    let accessToken = store.getters['user/accessToken']
    getUserInfo().then(userInfo => {
      console.log('userInfo', userInfo)
      if (accessToken) {
        config.headers.Authorization = `Bearer ${accessToken}`
      }
      loadingBar.start()
      console.log('after get userInfo api:', config.url)
      resolve(config)
    })
  })
}, function (error) {
  // Do something with request error
  loadingBar.close()
  return Promise.reject(error)
})

function getUserInfo () {
  if (!userInfoPromise) {
    console.log('begin get userInfo')
    userInfoPromise = new Promise(resolve => {
      setTimeout(() => {
        console.log('request get userInfo finished')
        resolve({aaa: 111})
      }, 5000)
    })
  }
  return userInfoPromise.then(info => info)
}
```

测试：

```
[HMR] Waiting for update signal from WDS...
api.js?93af:16 begin api: /api/web/um/user/queryUser
api.js?93af:38 begin get userInfo
NavMain.vue?d842:38 {name: "ManageAlumni", meta: {…}, path: "/PersonalOrganization/manageAlumni", hash: "", query: {…}, …}
api.js?93af:16 begin api: /api/web/um/student/queryPage
api.js?93af:41 request get userInfo finished
api.js?93af:21 userInfo {aaa: 111}
api.js?93af:26 after get userInfo api: /api/web/um/user/queryUser
api.js?93af:21 userInfo {aaa: 111}
api.js?93af:26 after get userInfo api: /api/web/um/student/queryPage
```