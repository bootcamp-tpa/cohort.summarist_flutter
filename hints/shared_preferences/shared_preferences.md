## トークンの確認
SummaristではSplashPageにてTokenが存在するかを確認します。Tokenはアクセストークンと言い、ログインする際にサーバから発行される値です。  
TokenはSharedPreferencesに保存することとします。SharedPreferencesはそのアプリの設定を保存する仕組みです。SharedPreferencesに保存した値は、アプリ終了後も消えません。  
SplashPageでは、SharedPreferencesからTokenを読み出します。  
その際、Tokenが存在している場合はログイン済みと見なしてHomePageに遷移します。Tokenが存在しない場合はSignInPageに遷移します。  

[画面遷移図](../../images/summarist_screens.jpg)で確認しておきましょう。  


### shared_preferencesのインストール
SharedPreferencesを使うには、Flutter公式が公開しているパッケージをインストールします。  

- [shared_preferences ](https://pub.dev/packages/shared_preferences)

ドキュメントにあるように、このパッケージを追加するにはプロジェクト直下で以下のコマンドを実行します。  

```
flutter pub add shared_preferences
```


### SharedPreferenceの読み書き
今回は、Tokenは`Token`という名前でSharedPreferencesに保存することとします。  

SharedPreferencesから`Token`を読み出すには以下のようにします。  

```dart
  Future<String?> _loadToken() async {
    final prefs = await SharedPreferences.getInstance();
    final token = prefs.getString('Token');
    return token;
  }
```

上記と[公式ドキュメント](https://pub.dev/packages/shared_preferences)を参考に、SharedPreferencesに書き出す関数も考えて見ましょう。  


### SplashPageでの処理
先ほどの`_loadToken()`メソッドを使うと、以下のようなロジックが書けます。  

```dart
_loadToken().then((token) {
  if (token == null || token.isEmpty) {
    // トークンが存在しないときの処理
  } else {
    // トークンが存在するときの処理
  }
});
```

以下のように、 `build()`メソッドに追加できます。  

```dart
class SplashPage extends StatelessWidget {
  const SplashPage({super.key});

  Future<String?> _loadToken() async {
    final prefs = await SharedPreferences.getInstance();
    final token = prefs.getString('Token');
    return token;
  }

  @override
  Widget build(BuildContext context) {
    _loadToken().then((token) {
      if (token == null || token.isEmpty) {
        // トークンが存在しないときの処理
      } else {
        // トークンが存在するときの処理
      }
    });
    // 省略
```

:pencil2: これまで置いていた3秒待つ処理は不要になりましたので、消しても構いません。  