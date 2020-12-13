# Today I Learned

* AWS-Cognito with React를 이용한 로그인 구현

---



## AWS-Cognito with React를 이용한 로그인 구현

[참고 동영상](https://www.youtube.com/watch?v=8WZmIdXZe3Q&t=352s)

[참고 사이트](https://blog.logrocket.com/authentication-react-apps-aws-amplify-cognito/)

1. AWS 콘솔 창에서 `Cognito` 생성

   ※ 각종 설정 및 설명은 동영상 참고

2. 생성된 `Pool ID`와 `Client ID`를 이용해 `Userpool.jsx` 생성

   * 해당 `jsx` 파일 안에는 `Cognito`에서 생성된 인증 키들을 담아 두는 곳임

* cognito 패키지 설치

```bash
$ npm i amazon-cognito-identity-js
```

* `Userpool.jsx`

```react
import { CognitoUserPool } from 'amazon-cognito-identity-js';

const poolData = {
  UserPoolId: 'us-east-1_EqzrkHsCr',
  ClientId: '34ue3045jjn78g6opfipmaud6d',
};

export default new CognitoUserPool(poolData);
```

3. 사용자 회원가입을 위한  `Singup.jsx` 생성
   * 사용자 아이디 / 비밀번호를 생성

```react
import React, { useState } from 'react';
import Userpool from './Userpool';

const Signup = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const onSubmit = (event) => {
    event.preventDefault();

    Userpool.signUp(email, password, [], null, (err, data) => {
      if (err) {
        console.error(err);
      }
      console.log(data);
    });
  };

  return (
    <div>
      <form onSubmit={onSubmit}>
        <label htmlFor="email">Email</label>
        <input
          value={email}
          onChange={(event) => setEmail(event.target.value)}
        />
        <label htmlFor="password">Password</label>
        <input
          value={password}
          onChange={(event) => setPassword(event.target.value)}
        />
        <button type="submit">Signup</button>
      </form>
    </div>
  );
};

export default Signup;
```

4. 사용자 로그인를 위한 `Login.jsx` 생성
   * 로그인을 하기 위해선 `Cognito 사용자 및 그룹` 탭에서 해당 사용자를 *CONFIRMED* 해줘야 함.

```react
import React, { useState } from 'react';
import { CognitoUser, AuthenticationDetails } from 'amazon-cognito-identity-js';
import Userpool from './Userpool';

const Login = () => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const onSubmit = (event) => {
    event.preventDefault();

    const user = new CognitoUser({
      Username: email,
      Pool: Userpool,
    });

    const authDetails = new AuthenticationDetails({
      Username: email,
      Password: password,
    });

    user.authenticateUser(authDetails, {
      onSuccess: (data) => {
        console.log('onSuccess: ', data);
      },
      onFailure: (err) => {
        console.error('onFailure: ', err);
      },
      newPasswordRequired: (data) => {
        console.log('newPasswordRequired: ', data);
      },
    });
  };

  return (
    <div>
      <form onSubmit={onSubmit}>
        <label htmlFor="email">Email</label>
        <input
          value={email}
          onChange={(event) => setEmail(event.target.value)}
        />
        <label htmlFor="password">Password</label>
        <input
          value={password}
          onChange={(event) => setPassword(event.target.value)}
        />
        <button type="submit">Login</button>
      </form>
    </div>
  );
};

export default Login;
```


