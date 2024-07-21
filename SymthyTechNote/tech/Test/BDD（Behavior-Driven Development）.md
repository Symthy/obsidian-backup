
## BDD（Behavior-Driven Development）とは

BDD（Behavior-Driven Development）は、TDD（Test-Driven Development）の進化形であり、ソフトウェアの動作（振る舞い）に焦点を当てて開発を行う手法です。BDDは、開発者、テスター、ビジネスアナリスト間のコミュニケーションを改善し、システムの望ましい振る舞いを明確にすることを目的としています。

### BDDの特徴

1. **ユーザーストーリーに基づいた開発**
   - BDDでは、ユーザーストーリーをベースにシステムの振る舞いを定義します。ユーザーストーリーは、システムがユーザーに提供すべき機能を簡潔に記述したものです。
   - 例: 「ユーザーとして、ログイン画面で正しい認証情報を入力することで、ダッシュボードにアクセスできる」

2. **Gherkin言語を使用した仕様記述**
   - BDDでは、Gherkinという自然言語に近い形式でテストシナリオを記述します。Gherkinは、「Given-When-Then」構文を使用して、条件、操作、期待される結果を明確にします。
   - 例:

```gherkin
Feature: User login

  Scenario: Successful login with valid credentials
    Given the user is on the login page
	When the user enters valid credentials
	Then the user should be redirected to the dashboard
```

3. **開発者と非技術者のコラボレーション**
   - BDDは、ビジネスの要求を理解しやすい形式で記述するため、開発者だけでなく、ビジネスアナリストやテスターも仕様の作成に参加できます。これにより、全員が同じ認識を共有しやすくなります。

### BDDの具体的なプロセス

1. **ユーザーストーリーの作成**
   - ビジネスアナリストやプロダクトオーナーが、システムが実現すべき機能をユーザーストーリーとして記述します。

2. **シナリオの記述**
   - 各ユーザーストーリーに対して、具体的なシナリオをGherkin言語で記述します。このシナリオは、Given-When-Then構文を使用して、前提条件、実行するアクション、期待される結果を示します。

3. **自動化テストの作成**
   - 開発者は、記述されたシナリオに基づいて自動化テストを作成します。これには、CucumberやSpecFlowなどのBDDツールを使用します。

4. **実装とテストの実行**
   - テストが失敗する状態で実装を開始し、テストがパスするようにコードを記述します。テストがパスしたら、リファクタリングを行い、コードを最適化します。

5. **反復**
   - このプロセスを反復し、システムのすべての機能を実装していきます。

### BDDのツール

- **Cucumber**: Gherkin言語を使用してシナリオを記述し、それを実行するためのフレームワーク。Java、Ruby、JavaScriptなど、さまざまな言語で使用できます。
- **SpecFlow**: .NET環境でBDDを実践するためのフレームワーク。Gherkin言語を使用してシナリオを記述します。
- **Behat**: PHPでのBDDを支援するフレームワーク。Gherkin言語を使用します。

### 具体例

以下は、Cucumberを使用した具体例です。

1. **Gherkinシナリオの記述**

```gherkin
Feature: User login

  Scenario: Successful login with valid credentials
    Given the user is on the login page
    When the user enters valid credentials
    Then the user should be redirected to the dashboard

  Scenario: Unsuccessful login with invalid credentials
    Given the user is on the login page
    When the user enters invalid credentials
    Then the user should see an error message
```

2. **ステップ定義の作成（Java）**

   ```java
   public class LoginSteps {

       private WebDriver driver;

       @Given("the user is on the login page")
       public void theUserIsOnTheLoginPage() {
           driver = new ChromeDriver();
           driver.get("http://example.com/login");
       }

       @When("the user enters valid credentials")
       public void theUserEntersValidCredentials() {
           driver.findElement(By.id("username")).sendKeys("validUser");
           driver.findElement(By.id("password")).sendKeys("validPassword");
           driver.findElement(By.id("loginButton")).click();
       }

       @When("the user enters invalid credentials")
       public void theUserEntersInvalidCredentials() {
           driver.findElement(By.id("username")).sendKeys("invalidUser");
           driver.findElement(By.id("password")).sendKeys("invalidPassword");
           driver.findElement(By.id("loginButton")).click();
       }

       @Then("the user should be redirected to the dashboard")
       public void theUserShouldBeRedirectedToTheDashboard() {
           assertEquals("http://example.com/dashboard", driver.getCurrentUrl());
           driver.quit();
       }

       @Then("the user should see an error message")
       public void theUserShouldSeeAnErrorMessage() {
           assertTrue(driver.findElement(By.id("errorMessage")).isDisplayed());
           driver.quit();
       }
   }
   ```

### まとめ

BDDは、ソフトウェアの振る舞いに焦点を当て、ユーザーストーリーやシナリオをもとに開発を行う手法です。これにより、開発者とビジネスアナリスト、テスター間のコミュニケーションが改善され、システムが期待通りに動作することを確実にします。BDDは、ユーザーストーリーの作成、Gherkin言語によるシナリオ記述、自動化テストの作成というステップを通じて実現されます。

## ユニットテストとBDDの違い

BDD（Behavior-Driven Development）は主にシステムの全体的な振る舞いをテストするための手法であり、ユニットテストよりも結合テストやシステムテストに近いアプローチです。以下のポイントで詳しく説明します。

### BDDの適用範囲

#### ユニットテストとBDDの違い

- **ユニットテスト**:
    
    - 個々のメソッドやクラスなど、システムの最小単位をテストします。
    - 内部ロジックや実装の詳細に焦点を当てます。
    - 外部依存関係はモックやスタブを使用して隔離します。
- **BDD**:
    
    - システムの機能や振る舞いをユーザーストーリーに基づいてテストします。
    - システム全体の動作や異なるコンポーネント間の相互作用をテストします。
    - テストは「Given-When-Then」の形式で記述され、ビジネスロジックやユーザーインターフェースの動作を検証します。

#### BDDのテストの対象

1. **結合テスト**:
    
    - 複数のユニットやコンポーネントが組み合わさった状態での動作をテストします。
    - BDDのシナリオは、異なるコンポーネントが連携して期待通りに動作することを確認するために使われます。
2. **システムテスト**:
    
    - 完全なシステムが一つのユニットとして正しく機能するかをテストします。
    - BDDは、ユーザーの観点からシステム全体の機能を検証します。