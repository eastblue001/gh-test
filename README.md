# Azure - GitHub 連携
## GitHub 組織IDの確認
### パーソナルアクセストークンの作成
1. 画面右上のユーザーアイコンから「Settings」をクリック
![pat01](img/gh-pat-01.png)
2. 画面左ペインの下の方にある「Developer Settings」をクリック
![pat01](img/gh-pat-02.png)
3. 画面左ペインの「Personal access tokens」-「Fine-grained tokens」をクリック
![pat01](img/gh-pat-03.png)
4. - 「Token name」に任意の名前を入力します。
   - 「Repository access」で「All repositories」を選択します。
   - 「Permissions」-「Add Permissions」を選択して、以下にチェックを入れて、「Generate token」を選択します。

      - Repository creation
      - Repository license policy
      - Repository security advisories
![pat01](img/gh-pat-04.png)
5. 確認画面が表示されたら、「Generate token」を選択します。
![pat01](img/gh-pat-05.png)
6. 作成されたトークンをコピーして保存します。
![pat01](img/gh-pat-06.png)

### GitHub 組織IDの確認
1. Windows Terminalを起動し、「コマンドプロンプト」を指定します。
2. 以下のリクエストを投げて、応答された「ID」をメモしておきます。
※これが「GitHub 組織ID」になります。
```shell
curl -H "Authorization: token YOUR_PERSONAL_ACCESS_TOKEN" https://api.github.com/orgs/YOUR_ORG_NAME
```
「YOUR_PERSONAL_ACCESS_TOKEN」の部分に先ほど作成したトークンを貼り付けます。
「YOUR_ORG_NAME」は、GitHub画面左上の組織名を指定します。
![ghid01](img/ghid-check-01.png)

## Repository IDの確認
### リポジトリの作成
1. 画面右上のユーザーアイコンから「Organizations」をクリック
2. 該当の組織名をクリック
3. 画面上部の「Repositories」をクリック
4. 「New repository」をクリック
![repoid01](img/repository-id-01.png)
5. 「Repository name」を入力して、「Add README」を"ON"にして「Create repository」をクリック
![repoid02](img/repository-id-05.png)

### Repository IDの確認
1. 上記の手順で作成されたリポジトリに遷移したら「Code」を選択して、<owner>/<repo>を確認します。
![repoid04](img/repo03.png)
2. 以下のリクエストを投げて、応答された「ID」をメモしておきます。
※これが「GitHub 組織ID」になります。
```shell
curl -H https://api.github.com/repos/<owner>/<repo>
```
![repoid04](img/ghid-check-02.png)
