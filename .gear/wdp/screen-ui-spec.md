# 画面UI定義書

```json
{
  "screens": [
    {
      "screenId": "SCR-001",
      "screenName": "ホーム（ダッシュボード）",
      "category": "ダッシュボード",
      "targetUser": "学生、教員、研究者",
      "overview": "研究トレンドの可視化グラフや主要な分析結果を俯瞰し、関心のある分野への入り口を提供するメイン画面。※要確認：レイアウト構成",
      "components": [
        {
          "name": "Header",
          "type": "header",
          "description": "ナビゲーションメニュー"
        },
        {
          "name": "Trend Chart",
          "type": "card",
          "description": "研究トレンドの時系列推移グラフ"
        },
        {
          "name": "Research Gap Summary",
          "type": "card",
          "description": "注目のResearch Gapへのリンクと簡略版ヒートマップ"
        },
        {
          "name": "Search Box",
          "type": "search-box",
          "description": "メイン検索用入力フィールド"
        }
      ],
      "operationSteps": [
        {
          "step": 1,
          "action": "検索キーワードを入力またはトレンドグラフをクリック",
          "systemResponse": "該当する検索結果画面または分析詳細へ遷移"
        }
      ],
      "fields": [
        {
          "name": "mainSearch",
          "type": "text",
          "required": false,
          "validation": "なし",
          "description": "研究論文検索用テキストボックス"
        }
      ],
      "events": [
        {
          "trigger": "検索ボタンクリック",
          "action": "論文検索・結果画面へ遷移",
          "description": "入力されたキーワードをクエリとして渡す"
        }
      ],
      "transitions": [
        {
          "action": "検索実行",
          "destination": "SCR-002",
          "condition": "キーワード入力あり"
        },
        {
          "action": "Gapカードクリック",
          "destination": "SCR-004",
          "condition": "なし"
        }
      ]
    },
    {
      "screenId": "SCR-002",
      "screenName": "論文検索・結果画面",
      "category": "トランザクション",
      "targetUser": "学生、教員、研究者",
      "overview": "論文をキーワードや属性で検索し、検索結果を一覧表示する画面。絞り込み機能を提供。",
      "components": [
        {
          "name": "Search Controls",
          "type": "search-box",
          "description": "高度な検索条件フィルタ（年度、著者、分野等）"
        },
        {
          "name": "Results Table",
          "type": "table",
          "description": "検索結果の一覧表示（論文タイトル、著者、年）"
        },
        {
          "name": "Pagination",
          "type": "pagination",
          "description": "検索結果のページネーション"
        }
      ],
      "operationSteps": [
        {
          "step": 1,
          "action": "検索条件を入力して検索",
          "systemResponse": "条件に合致する論文一覧を表示"
        },
        {
          "step": 2,
          "action": "特定の論文を選択",
          "systemResponse": "論文詳細画面へ遷移"
        }
      ],
      "fields": [
        {
          "name": "keyword",
          "type": "text",
          "required": false,
          "validation": "なし",
          "description": "キーワード検索"
        },
        {
          "name": "yearRange",
          "type": "date",
          "required": false,
          "validation": "開始年 <= 終了年",
          "description": "発行年による絞り込み"
        }
      ],
      "events": [
        {
          "trigger": "行クリック",
          "action": "詳細画面遷移",
          "description": "選択された論文IDをURLパラメータとして渡す"
        }
      ],
      "transitions": [
        {
          "action": "行選択",
          "destination": "SCR-003",
          "condition": "なし"
        }
      ]
    },
    {
      "screenId": "SCR-003",
      "screenName": "論文詳細画面",
      "category": "トランザクション",
      "targetUser": "学生、教員、研究者",
      "overview": "論文のメタデータ、抄録を表示し、AIによる要約や関連論文へのリンクを提供する画面。",
      "components": [
        {
          "name": "Paper Info",
          "type": "card",
          "description": "論文の基本情報（タイトル、著者、抄録）"
        },
        {
          "name": "AI Summary",
          "type": "card",
          "description": "AIによる要約セクション"
        },
        {
          "name": "Related Papers",
          "type": "table",
          "description": "関連論文リスト"
        },
        {
          "name": "Library Action",
          "type": "button-group",
          "description": "マイライブラリへの保存ボタン"
        }
      ],
      "operationSteps": [
        {
          "step": 1,
          "action": "AI要約ボタン押下",
          "systemResponse": "AIによる論文要約を表示"
        },
        {
          "step": 2,
          "action": "保存ボタン押下",
          "systemResponse": "マイライブラリに追加"
        }
      ],
      "fields": [],
      "events": [
        {
          "trigger": "保存ボタンクリック",
          "action": "API通信",
          "description": "ライブラリに追加するリクエスト送信"
        }
      ],
      "transitions": [
        {
          "action": "戻るボタン",
          "destination": "SCR-002",
          "condition": "なし"
        }
      ]
    },
    {
      "screenId": "SCR-004",
      "screenName": "Research Gap分析画面",
      "category": "レポート",
      "targetUser": "研究者、教員",
      "overview": "特定分野における研究の空白領域をヒートマップ等で視覚的に特定する分析画面。",
      "components": [
        {
          "name": "Heatmap View",
          "type": "card",
          "description": "研究分野と研究数の相関ヒートマップ"
        },
        {
          "name": "Filter Tabs",
          "type": "tabs",
          "description": "対象研究分野の切り替え"
        }
      ],
      "operationSteps": [
        {
          "step": 1,
          "action": "タブで分野選択",
          "systemResponse": "選択した分野のヒートマップを描画"
        }
      ],
      "fields": [
        {
          "name": "fieldSelection",
          "type": "select",
          "required": true,
          "validation": "未定義",
          "description": "分析対象の専門分野選択"
        }
      ],
      "events": [
        {
          "trigger": "ヒートマップエリアクリック",
          "action": "詳細ドリルダウン",
          "description": "該当領域の論文一覧へ"
        }
      ],
      "transitions": [
        {
          "action": "検索へ移動",
          "destination": "SCR-002",
          "condition": "なし"
        }
      ]
    },
    {
      "screenId": "SCR-005",
      "screenName": "マイライブラリ",
      "category": "トランザクション",
      "targetUser": "学生、教員、研究者",
      "overview": "保存した論文や作成したコレクションを閲覧・管理するユーザー専用ページ。",
      "components": [
        {
          "name": "Collection List",
          "type": "sidebar",
          "description": "保存先フォルダ一覧"
        },
        {
          "name": "Saved Papers",
          "type": "table",
          "description": "保存済み論文一覧"
        }
      ],
      "operationSteps": [
        {
          "step": 1,
          "action": "コレクション選択",
          "systemResponse": "該当コレクション内の論文を表示"
        },
        {
          "step": 2,
          "action": "論文削除ボタン押下",
          "systemResponse": "リストから削除"
        }
      ],
      "fields": [
        {
          "name": "collectionName",
          "type": "text",
          "required": true,
          "validation": "空文字不可",
          "description": "新規作成フォルダ名"
        }
      ],
      "events": [
        {
          "trigger": "削除ボタンクリック",
          "action": "削除確認モーダル表示",
          "description": "※要確認：削除フロー"
        }
      ],
      "transitions": [
        {
          "action": "タイトルクリック",
          "destination": "SCR-003",
          "condition": "なし"
        }
      ]
    }
  ]
}
```