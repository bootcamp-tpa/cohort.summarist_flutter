## SplashPage
まずはSplashPageを作成しましょう。  
SplashPageはアプリが起動する際に設定等を読み込むなどの準備を行う処理を記述します。  
しかし、この段階では特に行う処理はないため、単純に3秒待ってから他のページに遷移する実装にします。  

<img src="./images/splash_page.png" width="200px" />

### 3秒まってから遷移する
`Future.delayed`を使うと、未来に実行する処理を予約できます。  
これを使って3秒後に行う処理を予約します。

```dart
Future.delayed(const Duration(seconds: 3), () {
  // ここに3秒後に行う処理を書く
});
```

今回、3秒後に行う処理は画面遷移になります。そのためNavigatorを使うコードが入ります。  
SplashPageはアプリ起動直後に1度だけ表示する画面です。その特性上、アプリ起動後にこの画面に戻る必要はありません。（SplashPage以降の画面はすでに起動準備の処理が完了していることになるため。）  
そこで、`Navigator.push`の代わりに`Navigator.pushReplacement`を使いましょう。  
`Navigator.pushReplacement`により、「戻る」ボタンでこのSplashPageに戻れなくします。  

全体として、以下のようなコードになります。  


```dart
Future.delayed(const Duration(seconds: 3), () {
  Navigator.pushReplacement(
    context,
    MaterialPageRoute(
      builder: (context) => /* ここで遷移したいページのオブジェクトを作成 */,
    ),
  );
});
```

これで、3秒後にページ遷移を行う処理が完成しました。  
続いて、これをどこに書くか考えます。  
`Navigator.pushReplacement`を呼び出すにはBuildContextのオブジェクトが必要です。  
そこで、今回は`build()`メソッド内に書くことにします。  
なお、今回は`Future.delayed`に`await`は不要です。理由は以下の2点です。  

- `build()`は親クラスの同名メソッドをオーバーライドしたもののため、戻り値は`Widget`でなければなりません。`async/await`を使うと戻り値は`Future<Widget>`になります。メソッド名が同名であっても、戻り値が異なるため、オーバーライドできません。  
- 仮に`async/await`が使えたとしても、3秒待ってからWidgetをreturnすることになるため、それまで画面に何も表示されません。これはUI/UXとして不適切です。  

```dart
class SplashPage extends StatelessWidget {
  const SplashPage({super.key});

  @override
  Widget build(BuildContext context) {
    Future.delayed(const Duration(seconds: 3), () {
      Navigator.pushReplacement(
        context,
        MaterialPageRoute(
          builder: (context) => /* ここで遷移したいページのオブジェクトを作成 */,
        ),
      );
    });

    return const Scaffold(/* 省略 */);
  }
}
```