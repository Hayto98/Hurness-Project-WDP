# テーブル定義

| tableName | columnName | dataType | nullable | primaryKey | description |
| --- | --- | --- | --- | --- | --- |
| users | user_id | UUID | NO | YES | ユーザーID |
| users | username | VARCHAR(255) | NO | NO | ユーザー名 |
| users | email | VARCHAR(255) | NO | NO | メールアドレス |
| users | password_hash | VARCHAR(255) | NO | NO | パスワードハッシュ値 |
| users | role | VARCHAR(50) | NO | NO | ユーザー権限 (student, faculty, researcher, admin) ※要確認 |
| research_papers | paper_id | UUID | NO | YES | 論文ID |
| research_papers | title | TEXT | NO | NO | 論文タイトル |
| research_papers | abstract | TEXT | YES | NO | 抄録 |
| research_papers | doi | VARCHAR(255) | YES | NO | DOI (Digital Object Identifier) |
| research_papers | publish_year | INTEGER | NO | NO | 発行年 |
| research_papers | source_name | VARCHAR(255) | YES | NO | 取得元学術プラットフォーム名 |
| user_libraries | library_id | UUID | NO | YES | ライブラリID |
| user_libraries | user_id | UUID | NO | NO | ユーザーID (FK) |
| user_libraries | paper_id | UUID | NO | NO | 論文ID (FK) |
| user_libraries | folder_name | VARCHAR(255) | YES | NO | フォルダ名 ※要確認 |
| topic_subscriptions | subscription_id | UUID | NO | YES | 購読ID |
| topic_subscriptions | user_id | UUID | NO | NO | ユーザーID (FK) |
| topic_subscriptions | keyword | VARCHAR(255) | NO | NO | フォロー対象キーワード |
| analysis_logs | log_id | UUID | NO | YES | ログID |
| analysis_logs | analysis_type | VARCHAR(100) | NO | NO | 分析の種類 (トレンド, Gap等) ※未定義 |
| analysis_logs | execution_date | TIMESTAMP | NO | NO | 実行日時 |