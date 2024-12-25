# 日記アプリケーション要件定義書

## 1. 機能要件

### 1.1 ユーザー管理機能

*   ユーザー登録
*   ログイン/ログアウト
*   プロフィール管理（名前、メールアドレス）

### 1.2 日記機能

*   日記の作成
*   日記の編集
*   日記の一覧表示
*   日記の詳細表示
*   気分タグの追加（複数の気分タグを選択可能。過去に使用した気分タグをプルダウンで選択できる。テキスト入力も可能。入力されたタグは、既に存在する場合は既存のタグが利用され、存在しない場合は新規追加される。テキスト入力時には、過去に使用したタグがオートコンプリートで候補表示される。）
*   カテゴリータグの追加（複数のカテゴリータグを選択可能。過去に使用したカテゴリータグをプルダウンで選択できる。テキスト入力も可能。入力されたタグは、既に存在する場合は既存のタグが利用され、存在しない場合は新規追加される。テキスト入力時には、過去に使用したタグがオートコンプリートで候補表示される。）

## 2. 非機能要件

### 2.1 パフォーマンス

*   各APIのレスポンスタイムは、95パーセンタイルで500ms以内とする。
*   ユーザー数1万人、日記データ100万件の負荷に耐えられるようにする。

### 2.2 セキュリティ

*   パスワードはbcryptアルゴリズム（ストレッチング回数10）でハッシュ化して保存する。
*   JWTの有効期限は1時間とする。
*   入力値は、以下のバリデーションルールに従って検証する。
    *   ユーザー名：最大50文字
    *   メールアドレス：メールアドレス形式
    *   パスワード：8文字以上、大文字小文字英数字記号をそれぞれ1文字以上含む
    *   日記タイトル：最大100文字
    *   日記コンテンツ：最大10000文字
    *   気分タグ：最大20文字
    *   カテゴリータグ：最大20文字
*   SQLインジェクション、XSS、CSRF等の一般的なWebアプリケーションの脆弱性に対処する。

### 2.3 スケーラビリティ

*   データベースはPostgreSQLを使用し、シャーディングとレプリケーションによってスケーラビリティを確保する。
*   アプリケーションサーバーは、ロードバランサーによって負荷分散する。

### 2.4 ユーザビリティ

*   ユーザーインターフェースは、[デザインシステム名 or URL] に基づいて設計する。
*   エラーメッセージは、ユーザーにとって分かりやすく、問題解決に役立つ情報を提供する。

### 2.5 メンテナンス性

*   ログは、JSON形式で出力し、[ログ管理システム名] で集約・分析する。
*   エラー発生時は、エラーメッセージ、スタックトレース、および関連するコンテキスト情報をログに出力する。

## 3. 技術的制約

*   Node.js: 18以上
*   React: 18.2以上
*   Next.js: 13以上
*   Prisma: 4以上
*   shadcn/ui: 最新版
*   Tailwind CSS: 最新版
*   コーディング規約: Airbnb JavaScript Style Guideに従う
*   開発環境: Dockerの使用を必須とする
*   デプロイ: Vercelを使用する

## 4. テスト

*   テストカバレッジ目標:
    *   ユニットテスト: 80%以上
    *   結合テスト: 70%以上
*   テストデータは、`faker` ライブラリを使用して生成する。
*   テストは、コミット時、プルリクエスト時、およびデプロイ前に自動的に実行する。

## 5. AI-TDD

*   AIエージェントの役割:
    *   YAMLドキュメントと要件定義書に基づいて、コード（アプリケーションコード、テストコード）を生成する。
    *   テスト結果に基づいて、コードをリファクタリングする。
    *   コードレビューの結果に基づいて、プロンプトを改善する。
*   AIが生成したコードの評価基準:
    *   機能性: 要件定義書に記載された機能を満たしているか。
    *   可読性: コードが読みやすく、理解しやすいか。
    *   保守性: コードが変更や拡張に耐えうるか。
    *   効率性: コードが効率的に動作するか。
    *   テスト容易性: コードがテストしやすいか。
*   フィードバックループ:
    1. AIエージェントがコードを生成する。
    2. テスト駆動開発エンジニアがコードレビューとテストを実施する。
    3. AI駆動開発エンジニアがレビュー結果とテスト結果を評価し、YAMLドキュメントと要件定義書にフィードバックする。
    4. AI駆動開発エンジニアがフィードバックを基にプロンプトを改善する。
    5. 上記を繰り返す。
*   継続的改善: 2週間ごとにAI-TDDプロセスをレビューし、改善を行う。

## 6. 優先順位

*   高: ユーザー管理機能、日記の作成・編集・一覧表示・詳細表示
*   中: 気分タグ・カテゴリータグの追加
*   低: その他の機能

## 7. ER図

erDiagram
User ||--o{ Diary : "writes"
Diary ||--o{ DiaryMood : "has"
Diary ||--o{ DiaryTag : "has"
Mood ||--o{ DiaryMood : "belongs to"
Tag ||--o{ DiaryTag : "belongs to"

User {
    string id PK
    string name
    string email
    string password HASHED
    datetime createdAt
    datetime updatedAt
}

Diary {
    string id PK
    string title
    string content
    datetime createdAt
    datetime updatedAt
    string userId FK
}

DiaryMood {
    string diaryId FK
    string moodId FK
}

DiaryTag {
    string diaryId FK
    string tagId FK
}

Mood {
    string id PK
    string name UNIQUE
}

Tag {
    string id PK
    string name UNIQUE
}

## 8. OpenAPI
openapi: 3.0.0
info:
  title: 日記アプリケーションAPI
  version: 1.0.0

paths:
  /api/auth/signup:
    post:
      summary: ユーザー登録
      tags: [認証]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                email:
                  type: string
                  format: email
                password:
                  type: string
                  minLength: 8
                name:
                  type: string
              required:
                - email
                - password
                - name
      responses:
        '201':
          description: ユーザー登録成功
        '400':
          description: リクエストが不正

  /api/auth/signin:
    post:
      summary: ログイン
      tags: [認証]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                email:
                  type: string
                  format: email
                password:
                  type: string
              required:
                - email
                - password
      responses:
        '200':
          description: ログイン成功
          content:
            application/json:
              schema:
                type: object
                properties:
                  token:
                    type: string
                    description: JWTトークン
        '400':
          description: リクエストが不正
        '401':
          description: 認証情報が不正

  /api/auth/signout:
    post:
      summary: ログアウト
      tags: [認証]
      security:
        - bearerAuth: []
      responses:
        '204':
          description: ログアウト成功

  /api/diaries:
    get:
      summary: ログインユーザーの日記一覧取得
      tags: [日記]
      security:
        - bearerAuth: []
      responses:
        '200':
          description: 日記一覧取得成功
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Diary'
        '401':
          description: 認証エラー
    post:
      summary: 日記作成
      tags: [日記]
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                title:
                  type: string
                content:
                  type: string
                moodIds:
                  type: array
                  items:
                    type: string
                tagIds:
                  type: array
                  items:
                    type: string
              required:
                - title
                - content
                - moodIds
                - tagIds
      responses:
        '201':
          description: 日記作成成功
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Diary'
        '400':
          description: リクエストが不正
        '401':
          description: 認証エラー

  /api/diaries/{id}:
    get:
      summary: ログインユーザーの日記詳細取得
      tags: [日記]
      security:
        - bearerAuth: []
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: 日記詳細取得成功
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Diary'
        '401':
          description: 認証エラー
        '404':
          description: 日記が見つからない
    put:
      summary: 日記更新
      tags: [日記]
      security:
        - bearerAuth: []
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                title:
                  type: string
                content:
                  type: string
                moodIds:
                  type: array
                  items:
                    type: string
                tagIds:
                  type: array
                  items:
                    type: string
      responses:
        '200':
          description: 日記更新成功
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Diary'
        '400':
          description: リクエストが不正
        '401':
          description: 認証エラー
        '404':
          description: 日記が見つからない

  /api/moods:
    get:
      summary: 気分タグ一覧取得（過去に使用されたものを含む）
      tags: [気分タグ]
      responses:
        '200':
          description: 気分タグ一覧取得成功
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Mood'

  /api/tags:
    get:
      summary: カテゴリータグ一覧取得（過去に使用されたものを含む）
      tags: [カテゴリータグ]
      responses:
        '200':
          description: カテゴリータグ一覧取得成功
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Tag'

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
  schemas:
    Diary:
      type: object
      properties:
        id:
          type: string
        title:
          type: string
        content:
          type: string
        createdAt:
          type: string
          format: date-time
        updatedAt:
          type: string
          format: date-time
        userId:
          type: string
        moods:
          type: array
          items:
            $ref: '#/components/schemas/Mood'
        tags:
          type: array
          items:
            $ref: '#/components/schemas/Tag'
    Mood:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
    Tag:
      type: object
      properties:
        id:
          type: string
        name:
          type: string