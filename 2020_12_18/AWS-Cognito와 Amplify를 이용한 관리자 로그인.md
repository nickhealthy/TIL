# Today I Learned

* AWS-Cognito와 Amplify를 이용한 관리자 로그인 페이지 구현

---



## AWS-Cognito와 Amplify를 이용한 관리자 로그인 페이지 구현

관리자 페이지는 인가된 사용자만 해당 서비스를 이용할 수 있게 해야함

* Amplify를 통해 로그인 이후, 홈 화면이 보여지도록 설정



1. 필요 패키지 설치

```bash
$ npm install aws-amplify @aws-amplify/ui-react
$ npm install aws-amplify
```

2. AWS에서 설정한 cognito 설정 정보 등록

`Userpool.jsx`

```react
export default {
  Auth: {
    region: 'us-east-1',
    userPoolId: '풀ID',
    userPoolWebClientId: '클라이언트ID',
  },
};
```

3. 로그인 이후, 서비스가 시작 될 페이지에 import

`App.js`

```react
import Amplify from 'aws-amplify';
import { AmplifyAuthenticator, AmplifySignIn } from '@aws-amplify/ui-react';
import Userpool from './components/Authentication/Userpool';

// Amplify - Cognito 설정
Amplify.configure(Userpool);
```

4. `APP.js`의 render() 함수 부분

```react
<AmplifyAuthenticator>
  <Home />
</AmplifyAuthenticator>
```

5. 로그인 페이지 커스텀 마이징
   * `AmplifySignIn`에 들어있는 속성들은 정해진 값들임

```react
<AmplifyAuthenticator>
<AmplifySignIn
      slot="sign-in"
      headerText="따릉이 Sign In"
      usernameAlias="email"
      style={{
      display: 'flex',
      justifyContent: 'center',
      }}
  >
  <div slot="secondary-footer-content"></div>
 </AmplifySignIn>
   <Home />
</AmplifyAuthenticator>
```

6. 로그아웃 버튼

`Home.jsx`

```react
import React from 'react';
import { AmplifySignOut } from '@aws-amplify/ui-react';

function Home() {
  return (
    <>
      <h1>Home</h1>
      <p>Welcome home!</p>
      <div style={{ width: '10%', float: 'right' }}>
        <AmplifySignOut buttonText="Sign Out" />
      </div>
    </>
  );
}

export default Home;
```

7. 로그인, 로그아웃 버튼 CSS 설정

`App.css`

```css
:root {
  /* 중간 */
  --amplify-primary-color: #6a7ca2;
  /* 연하게 */
  --amplify-primary-tint: #7d8fb6;
  /* 진하게 */
  --amplify-primary-shade: #55627e;
}
```



#### 전체 소스코드

`App.js` (amplify - cognito 설정)

```react
import Amplify from 'aws-amplify';
import { AmplifyAuthenticator, AmplifySignIn } from '@aws-amplify/ui-react';
import Userpool from './components/Authentication/Userpool';

Amplify.configure(Userpool);

<AmplifyAuthenticator>
  <AmplifySignIn
    slot="sign-in"
    headerText="따릉이 Sign In"
    usernameAlias="email"
    style={{
    display: 'flex',
    justifyContent: 'center',
    }}
  >
  <div slot="secondary-footer-content"></div>
  </AmplifySignIn>
    <Home />
  </AmplifyAuthenticator>
```

`App.css` (로그인, 로그아웃 버튼 설정)

```css
:root {
  /* 중간 */
  --amplify-primary-color: #6a7ca2;
  /* 연하게 */
  --amplify-primary-tint: #7d8fb6;
  /* 진하게 */
  --amplify-primary-shade: #55627e;
}
```

`Home.jsx` (로그아웃 버튼)

```react
import React from 'react';
import { AmplifySignOut } from '@aws-amplify/ui-react';

function Home() {
  return (
    <>
      <h1>Home</h1>
      <p>Welcome home!</p>
      <div style={{ width: '10%', float: 'right' }}>
        <AmplifySignOut buttonText="Sign Out" />
      </div>
    </>
  );
}

export default Home;
```

