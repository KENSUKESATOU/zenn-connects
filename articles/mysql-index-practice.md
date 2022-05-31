---
title: ""
emoji: "🎉"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

この記事はSQLのインデックスが効くパターンと効かないパターンをまとめたものです。


中間一致・後方一致のLIKE文
SELECT ID FROM USER WHERE USER_NAME LIKE '%岡賢%'
SELECT ID FROM USER WHERE USER_NAME LIKE '%岡賢一' 

インデックス列で演算をやっている
SELECT NAME FROM GOODS WHERE PRICE * 10 > 100000 

IS NULLを使っている＜＝要確認　https://notepad-blog.com/content/31/　https://techblog.istyle.co.jp/archives/1514 では効くと記載あり 
SELECT NAME FROM GOODS WHERE PRICE IS NULL

インデックス列に対して関数を使っている
SELECT NAME FROM GOODS WHERE MAX(PRICE) = 100000

インデックス列に否定形を使っている
SELECT NAME FROM GOODS WHERE PRICE <> 100000

万単位のレコード、10～5%程度の該当率でないとINDEXは効かない

カーディナリティ(一意性)が十分でないとINDEXは効かない＜＝？
テストによる大量のINSERTやUPDATEなどで統計情報が現実に即していないと効かないこの場合は統計情報をリセットする＜＝？

OR

検索カラムがインデックスの第2カラム以降のみ

複合インデックスの第1カラムを使っていても、指定する順番を間違えている
　「第1カラム = **　AND　第2カラム = **」 => インデックスが効く
　「第2カラム = **　AND　第1カラム = **」 => インデックスが効かない

日付型(TIMESTAMPとDATETIME)でLIKE検索

IN句とサブクエリの組み合わせ＜＝？

暗黙の型変換を利用している
EXPLAIN SELECT * FROM users WHERE age  < '21' \G;



効くパターン
検索カラムがインデックスの第1カラム
数値 or 文字列型数値で等号/不等号条件
数値型 or 文字列型(日付型 以外)で等号/不等号/IN句/BETWEEN句/前方一致のLIKE検索
日付型(TIMESTAMPとDATETIME) で等号/不等号/IN句/BTWEEN句
AND条件
IS NULL/IS NOT NULL( MySQL 4.1.22 )


https://qiita.com/NagaokaKenichi/items/44cabcafa3d02d9cd896
https://www.yuulinux.tokyo/8205/
https://notepad-blog.com/content/31/
https://nishinatoshiharu.com/index-not-work/