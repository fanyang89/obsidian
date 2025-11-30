```sql
SELECT decode(DATA)->'user' AS USER, COUNT(TXT) AS MSG_COUNT FROM message WHERE USER IS NOT NULL GROUP BY USER ORDER BY MSG_COUNT DESC LIMIT 10;
┌───────────────┬───────────┐
│     USER      │ MSG_COUNT │
│     json      │   int64   │
├───────────────┼───────────┤
│ "U04235CS33R" │    183177 │ warrior
│ "U2UQVUA87"   │    159190 │ fengli
│ "U0C1HMB2P"   │     48052 │ paula
│ "U04B472CV"   │     42095 │ kyle
│ "U0F3BU9RR"   │     30795 │ yiran
│ "U04B2HF0K"   │     30647 │ wenhao
│ "U0FFS5R5H"   │     28135 │ qiuping
│ "U0B9XPT97"   │     21998 │ guojian
│ "U0GL81H8C"   │     16657 │ keyue
│ "U0SPZH0SV"   │     15770 │ yutian
├───────────────┴───────────┤
│ 10 rows         2 columns │
└───────────────────────────┘


-- on zbs channel
┌─────────────┬───────────┐
│    USER     │ MSG_COUNT │
│    json     │   int64   │
├─────────────┼───────────┤
│ "U04B472CV" │     14188 │ kyle
│ "U04B2HF0K" │      9033 │ wenhao
│ "U0GL81H8C" │      8222 │ keyue
│ "U0SPZH0SV" │      8090 │ yutian
│ "U2UQVUA87" │      5006 │ fengli
│ "U0F3BU9RR" │      3247 │ yiran
│ "U0FFS5R5H" │      3133 │ qiuping
│ "UEBE4B3U7" │      2689 │ fanyang
│ "UAK57FDSP" │      2450 │ wangsai
│ "U06UEKUF3" │      2417 │ mengran
├─────────────┴───────────┤
│ 10 rows       2 columns │
└─────────────────────────┘

ID           USERNAME
-----------  -------------
UEBE4B3U7    fanyang        8674
UUD66RM97    weipeng.zhu    1463
U01CDF2GH5M  dongdong.li     275
U01F99QHZ5Z  tengqiu.yu      980
U01UBHRV0SZ  yiwu.cai        674
U02E42825RC  peng.lian       539
U02RP8FU8K0  haoqian.he      944
U032UHH8LER  xi.gou         1100
U0357MYTWR0  kaiqi.chen      376
U03HRQ5LP50  pinghao.liang   285
U03LA0JQ491  sijie.sun       965
U03PBHRT63T  tianze.zhang    247
U04P1NKU7PY  shunkai.zhai    171
U054HH0GGNN  haiwei.deng      42
U05B0909ZRR  zehua.qi        422

SELECT decode(DATA)->'user' AS USER, COUNT(TXT) AS MSG_COUNT
FROM message
WHERE (SELECT USER IN
['"UEBE4B3U7"', '"UUD66RM97"', '"U01CDF2GH5M"', '"U01F99QHZ5Z"', '"U01UBHRV0SZ"', '"U02E42825RC"', '"U02RP8FU8K0"', '"U032UHH8LER"', '"U0357MYTWR0"', '"U03HRQ5LP50"', '"U03LA0JQ491"', '"U03PBHRT63T"', '"U04P1NKU7PY"', '"U054HH0GGNN"', '"U05B0909ZRR"']
)
GROUP BY USER
ORDER BY MSG_COUNT DESC;

D SELECT ANY_VALUE(TXT), COUNT(TXT) AS MSG_COUNT FROM message WHERE TXT != '' GROUP BY TXT ORDER BY MSG_COUNT DESC LIMIT 30;
```
