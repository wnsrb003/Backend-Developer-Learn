# 미들웨어
미들웨어 함수는 req(요청) 객체, res(응답) 객체, 그리고 어플리케이션 요청-응답 사이클 도중 그 다음의 미들웨어 함수에 대한 엑세스 권한을 갖는 함수이다.

미들웨어란 간단하게 말하면 클라이언트에게 요청이 오고 그 요청을 보내기 위해 응답하려는 중간(미들)에 목적에 맞게 처리를 하는, 말하자면 거쳐가는 함수들이라고 보면 되겠다.

예를 들어서 요청-응답 도중에 시간을 콘솔 창에 남기고 싶으면 미들웨어 함수를 중간에 넣어서 표시를 한 뒤에 계속해서 다음 미들웨어들을 처리할 수 있도록 하는 것이다.

다음 미들웨어 함수에 대한 엑세스는 next 함수를 이용해서 다음 미들웨어로 현재 요청을 넘길 수 있다.

next라는 말에서 알 수 있듯이 next를 통해 미들웨어는 순차적으로 처리된다. (따라서 순서가 중요하다!!)

## 예시
- 로그인 check 미들웨어 - isLoggedIn, isNotLoggedIn 이라는 미들웨어 생성
```
exports.isLoggedIn = (req, res, next) => {
  if (req.isAuthenticated()){
    next();
  } else {
    res.status(403).send('로그인 필요');
  }
};

exports.isNotLoggedIn = (req, res, next) => {
  if (!req.isAuthenticated()){
    next();
  } else {
    const message = encodeURIComponent('로그인한 상태');
    res.redirect(`/?error=${message}`);
  }
};
```
- 페이지 요청 전에 로그인이 된 사용자인지 아닌지 판별을 위한 미들웨어 삽입 
```
router.get('/profile', isLoggedIn, (req, res) => {
  res.render('porfile', { title: '내 정보 - NodeBird' });
});

router.get('/join', isNotLoggedIn, (req, res) => {
  res.render('join', { title: '회원가입 - NodeBird' });
});

```
- 로그인 요청에서 미들웨어 내의 미들웨어 사용 예시
```
router.post('/login', isNotLoggedIn, (req, res, next) => {
  passport.authenticate('local', (authError, user, info) => {
    if (authError) {
      console.error(authError);
      return next(authError);
    }
    if (!user) {
      return res.redirect(`/?loginError=${info.message}`);
    }
    return req.login(user, (loginError) => {
      if (loginError) {
        console.error(loginError);
        return next(loginError);
      }
      return res.redirect('/');
    });
  })(req, res, next); // 미들웨어 내의 미들웨어에는 (req, res, next)를 붙입니다.
});
```
