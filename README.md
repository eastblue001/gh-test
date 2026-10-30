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

## サービスプリンシパルの作成
### サービスプリンシパルの作成
1. Microsoft Entra IDにアクセスし、左ブレードから「アプリの登録」を選択し、右ペインで「新規登録」を選択します。
![sp01](img/sp01.png)
2. 名前を入力し、「シングル テナントのみ」を選択して、「登録」を選択
![sp02](img/sp02.png)
3. ページが遷移されたら「アプリケーション(クライアント)ID」、「ディレクトリID」をメモしておきます。
![sp03](img/sp03.png)
4. 左ブレードから「証明書とシークレット」を選択し、右ペインで「フェデレーション資格情報」タブで、[資格情報の追加] を選択します。
![sp04](img/sp04.png)
5. 「フェデレーション資格情報のシナリオ」で「Azure リソースをデプロイする GitHub Actions」を選択し、下記の情報を入力します。

   - 「組織」にGitHubの組織名を入力します。
   - 「Organization ID」に手順「GitHub 組織IDの確認」でメモした値を入力します。
   - 「リポジトリ」に作成した「リポジトリ名」を入力します。
   - 「Repository ID」に手順「Repository IDの確認」でメモした値を入力します。
   - 「エンティティ型」で「ブランチ」を選択します。
   - 「GitHub ブランチ名」で「*」を入力します。
   - 「資格情報の詳細」の配下の「名前」で「リポジトリ名-Secret」で入力します。
   - 「追加」を選択します。
![sp05](img/sp05.png)

### Azureロールの割り当て
1. Azure Portalにてサブスクリプションを選択します。
![rbac01](img/rbac01.png)
2. XXXサブスクリプションを選択します。
![rbac02](img/rbac02.png)
3. サブスクリプションIDをメモしておきます。
4. 左ブレードから「アクセス制御（IAM）」を選択し、右ペインで「追加」－「ロール割り当ての追加」を選択します。
5. 「特権管理者ロール」-「共同作成者」を選択し「次へ」を選択します。
![rbac03](img/rbac03.png)
6. 「+ メンバーを選択する」を選択し手順(サービスプリンシパルの作成)で作成したサービスプリンシパル名を選択して「次へ」を選択します。
![rbac04](img/rbac04.png)
7. 割り当てのタイプで「アクティブ」、割り当て期間で「Permanent」にチェックを入れ「レビューと割り当て」を選択します。
![rbac05](img/rbac05.png)
8. 内容を確認し「レビューと割り当て」を選択します。
![rbac06](img/rbac06.png)

## GitHub - Azure 連携
1. GitHub Enterpriseの画面に戻ります。
2. 画面上部から「リポジトリ」を選択し、該当の「リポジトリ名」を選択します。
![ghe01](img/ghe01.png)
3. 画面上部の「Settings」を設定し、左ペインの「Secrets and variables」-「Actions」を選択します。
![ghe02](img/ghe02.png)
4. Repository secrets のボックス内にある「New repository secret」を選択します。
![ghe03](img/ghe03.png)
5. AZURE_CLIENT_ID、AZURE_TENANT_ID、AZURE_SUBSCRIPTION_ID のシークレットを作成します。

   - Nameを「AZURE_CLIENT_ID」
     「Secret」にサービスプリンシパルの作成手順でメモした「アプリケーション(クライアント)ID」を貼り付けて「Add Secret」を選択します。
   - Nameを「AZURE_TENANT_ID」
     「Secret」にサービスプリンシパルの作成手順でメモした「ディレクトリID」を貼り付けて「Add Secret」を選択します。
   - Nameを「AZURE_SUBSCRIPTION_ID」
     「Secret」にAzureロールの割り当ての作成手順でメモした「サブスクリプションID」を貼り付けて「Add Secret」を選択します。
![ghe04](img/ghe04.png)

## GitHub Actions の動作確認
1. 画面上部から「Actions」を選択し、Simple workflow で「Configure」を選択します。
![gha01](img/gha01.png)
2. 表示されるコードを全て消して、XXコードを貼り付けて、左上の「Commit changes」をクリックします。
![gha02](img/gha02.png)

```yml
name: Azure Login Test
on:
  workflow_dispatch:

permissions:
      id-token: write
      contents: read
      
jobs: 
  Azure-Login-Test:
    runs-on: ubuntu-latest
    steps:
    - name: 'Az CLI login'
      uses: azure/login@a641126d1b8aa4d1fa005f4f92df94a3a4c4c906 # v3.1.0
      with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
  
    - name: 'Run Azure CLI commands'
      run: |
          az account show
          az group list
```

4. Commit Message に「First GitHub Actions Test」と入力して「Commit changes」をクリックします。
![gha03](img/gha03.png)
5. 画面上部から「Actions」を選択し、左ペインから「Azure Login Test」を選択し、右ペインの「Run workflow」を選択します。
![gha04](img/gha04.png)
6. 実行が開始され、成功（緑色のマーク）に変わることを確認します。
![gha05](img/gha05.png)
7. 「Azure Login Test」を選択し、リソースグループの一覧が表示されることを確認します。
![gha06](img/gha06.png)
