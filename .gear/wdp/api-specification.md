# API仕様書

| endpoint | method | description | requestBody | responseBody | auth |
| --- | --- | --- | --- | --- | --- |
| /api/auth/login | POST | メールアドレスとパスワードによる認証を行い、アクセストークンを発行する。 | [object Object] |  | [object Object] |
| /api/papers | GET | キーワードや属性を用いて論文を検索する。 |  |  | [object Object] |
| /api/papers/{id} | GET | 論文の詳細情報を取得する。 |  |  | [object Object] |
| /api/papers/{id}/summary | GET | AIによる論文の要約を取得する。※要確認：利用するLLM API |  |  | [object Object] |
| /api/analysis/trends | GET | 年度別の論文数統計を取得する。 |  |  | [object Object] |
| /api/libraries/papers | POST | マイライブラリに論文を追加する。 | [object Object] |  | [object Object] |
| /api/admin/sources | POST | 外部ソースから論文を収集するバッチをキックする。※要確認：権限設定 | [object Object] |  | [object Object] |