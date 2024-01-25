## SignInPage: ログイン処理

サーバーには、予め以下のテスト用のアカウントが登録されています。  

Email:
```
user@domain.com
```

Password:
```
password123
```

まずは、サーバのドキュメントから、サーバのURLは以下となります。  

```
http://localhost:8080/auth/sign-in
```

続いて、このエンドポイント（URL）はPOSTメソッドで以下のデータを送信することが書かれてあります。  

```json
{
  "email": String,
  "password": String
}
```

さらに、レスポンスは次の用になります。  

```json
{
  "token": "String"
}
```

リクエスト、レスポンス、それぞれのデータはJSON形式です。  
DartではJSONをMapに変換できますが、Mapのままではプログラムで使いにくいうえ、保守性が低下します。（サーバの仕様変更に伴い、Flutterアプリ側の書き換え箇所が多いためです。）  
そこで、独自のDartクラスにデータを載せ替えます。  
そのためのクラスを作成しておきましょう。  

```dart
//リクエストを表すクラス
class SignInForm {
  final String email;
  final String password;

  SignInForm({
    required this.email,
    required this.password,
  });

  Map<String, dynamic> toJson() {
    return {
      'email': email,
      'password': password,
    };
  }
}

// レスポンスを表すクラス
class SignInResponse {
  String token;

  SignInResponse({
    required this.token,
  });

  SignInResponse.fromJson(Map<String, dynamic> json) : token = json['token'];
}
```

通信するコードは以下のようになります。  

```dart
Future<void> _submitForm() async {
  final signInForm = SignInForm(email: _email, password: _password);
  try {
    final url = Uri.parse('http://localhost:8080/auth/sign-in');
    
    // POSTメソッドでデータを送信
    final http.Response response =
        await http.post(url, body: jsonEncode(signInForm.toJson()));
    
    // レスポンスをMapに変換
    final decodedBody = jsonDecode(response.body);
    
    // Mapのデータを独自のSignInResponseクラスに載せ替え
    final signInResponse = SignInResponse.fromJson(decodedBody);
    
    // ここでtokenの値をSharedPreferencesに保存するなど行う
  } catch (e) {
    throw Exception(e.toString());
  }
}
```

あとは上記の通信のコードを送信ボタンのタップに合わせて実行する。  
`token`が空だった場合は何らかのEmailやPasswordの入力ミスの可能性がある。  


SignUpPageも同様の手順で実装できる。  
こちらは自分で試してみよう。  