---
title: "[Flask, Vue] Cross origin 해결"
tags: flask vue troubeshooting
---

CORS(Cross Origin Resource Sharing)란 도메인과 포트가 다른 서버로 client가 요청했을때 브라우저가 보안상 이유로 API를 차단하는 문제이다.

동일출처 정책으로 같은 도메인이 아니면 받는 server에서 모든 client 요청에 대한 cross-origin HTTP를 허가해주지 않는다면 브라우저단에서 cors에러가 발생한다.

<!--more-->

## Backend

```sh
pip install flask-cors
```

```py
from flask_cors import CORS

app = Flask(__name__)
CORS(app)
```

## Frontend

### 1. proxy

`vue.config.js`에 proxy로 사용할 api 도메인 설정

#### `vue.config.js`

```js
// single backend
module.exports = {
    devServer: {
        proxy: 'https://localhost:5000',
    },
};

// multiple backend
module.exports = {
    devServer: {
        proxy: {
            // v1 으로 시작하는 소스는 자동으로 target을 잡아준다
            'v1': {
                target: 'http://localhost:8888',
                changeOrigin: true,
                pathRewrite: {
                    '^/v1': ''
                }
            },
            'v2': {
                target: 'http://localhost:9999',
                changeOrigin: true,
                pathRewrite: {
                    '^/v2': ''
                }
            }
        }
    },
};
```

#### use

```js
await axios.get('/v1/feed', params);
```

axios를 쓸 때 proxy를 거쳐 target으로 가기 때문에, 별도로 target url을 쓰지 않는다. 루트 도메인을 제외한 나머지 path를 적어준다.

### 2. /

서버에 cors 설정이 되어 있지만 여전히 CORS 문제가 발생했다. API의 링크 끝에 슬래시(/)를 추가했더니 CORS 오류 없이 잘 동작함.
이유를 모르겠다.

```js
const axiosIns = axios.create({
  baseURL: 'http://localhost:5000/',
  timeout: 1000,
  headers: { 
    'Access-Control-Allow-Origin': '*',
    'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS'
  },
});

// cors error
await axiosIns.get('/feed');

// no error
await axiosIns.get('/feed/');
```

## Reference

- [vue-axios-cors-policy-no-access-control-allow-origin](https://stackoverflow.com/questions/55883984/vue-axios-cors-policy-no-access-control-allow-origin)
