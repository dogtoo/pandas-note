# pandas-note
記錄特殊情況所下的指令

# 多筆重覆資料（主檔跟明細 unit），要將主檔資料取出
```python
print('====df====')
print(df[['decl_no', 'item_no', 'broker_box_no', 'qty']][:10])
print('====exdf====')
print(exdf[['decl_no', 'broker_box_no']][:10])

g = exdf.groupby(['decl_no'])
exdf = g.first().reset_index()
print('====exdf after group====')
print(exdf[['decl_no', 'broker_box_no']][:10])
```

```python
====df====
          decl_no item_no broker_box_no     qty
0  DA  10210Y1120       5           210    1000
1  DA  10210Y1120       6           210    1000
2  DA  10210Y1120       7           210    5800
3  DA  10210Y1120       8           210   15000
4  DA  10210Y1120       9           210   10000
5  DA  10210Y1120      10           210    9100
6  DA  10210Y1120      11           210  155000
7  DA  10210Y1120      12           210    5000
8  DA  10210Y1120      13           210   25000
9  DA  10210Y1120      14           210    4500
====exdf====
          decl_no broker_box_no
0  DA  10210Y1120           210
1  DA  10210Y1120           210
2  DA  10210Y1120           210
3  DA  10210Y1120           210
4  DA  10210Y1120           210
5  DA  10210Y1120           210
6  DA  10210Y1120           210
7  DA  10210Y1120           210
8  DA  10210Y1120           210
9  DA  10210Y1120           210
====exdf after group====
          decl_no broker_box_no
0  AA  10008E9004             8
1  AA  10008E9005             8
2  AA  10028H7313            28
3  AA  10037RB052            37
4  AA  10039F0734            39
5  AA  10039F0735            39
6  AA  10051F2394            51
7  AA  10051F2401            51
8  AA  10051F2402            51
9  AA  10051F2431            51
```

# 同一欄位，不同日期格式
```python
print('===date & datetime===')
print(df['NEW_EXAM_DATE'][:60])
print('===change===')
df['NEW_EXAM_DATE'] = pd.to_datetime(df['NEW_EXAM_DATE'])
print(df['NEW_EXAM_DATE'][:60])
```

```
===date & datetime===
10    2021/9/28 12:14:40 AM
11    2021/9/28 12:14:40 AM
12    2021/9/28 12:14:40 AM
13                2021/9/28
14                2021/9/28
15                2021/9/28
16                2021/9/28
53    2021/9/28 01:20:12 PM
54    2021/9/28 01:20:12 PM
55    2021/9/28 01:20:12 PM
56    2021/9/28 01:20:12 PM
57                2021/9/28
===change===
10   2021-09-28 00:14:40
11   2021-09-28 00:14:40
12   2021-09-28 00:14:40
13   2021-09-28 00:00:00
14   2021-09-28 00:00:00
15   2021-09-28 00:00:00
16   2021-09-28 00:00:00
53   2021-09-28 13:20:12
54   2021-09-28 13:20:12
55   2021-09-28 13:20:12
56   2021-09-28 13:20:12
57   2021-09-28 00:00:00
```

# regex 比對取值後重組
```python
#原始資料是 2021/9/28 上午 12:14:40 => 要轉成 2021/9/28 12:14:40 AM 但欄位裡又有 2021/9/28 這樣格式的資料
#先換掉上下午
df['NEW_EXAM_DATE'] = df['NEW_EXAM_DATE'].str.replace("上午", 'AM')
df['NEW_EXAM_DATE'] = df['NEW_EXAM_DATE'].str.replace("下午", 'PM')
df['NEW_EXAM_DATE_N'] = '' #<==這做法不好，要用loc
#str.extract 比對到 yyyy/mm/dd, AM|PM, hh:mi:ss 後取出陣列
print('===date & datetime===')
print(df['NEW_EXAM_DATE'].str.extract(r'(\d{4}\/\d{1,2}\/\d{1,2})(.*(\s(AM|PM))(.*))?')[:20])
#然後使用str.cat 組合回去（只挑0,4,2欄組回), 空值的部份要設na_rep='', 不然有出現NaN的raw會沒資料
print('===change===')
df['NEW_EXAM_DATE'] = df['NEW_EXAM_DATE_N'].str.cat(df['NEW_EXAM_DATE'].str.extract(r'(\d{4}\/\d{1,2}\/\d{1,2})(.*(\s(AM|PM))(.*))?')[[0,4,2]], na_rep='')
print(df['NEW_EXAM_DATE'][:60])
```

```python
===date & datetime===
            0             1    2    3          4
0   2021/9/28   AM 12:14:40   AM   AM   12:14:40
1   2021/9/28   AM 12:14:40   AM   AM   12:14:40
2   2021/9/28   AM 12:14:40   AM   AM   12:14:40
...
13  2021/9/28           NaN  NaN  NaN        NaN
14  2021/9/28           NaN  NaN  NaN        NaN
15  2021/9/28           NaN  NaN  NaN        NaN
16  2021/9/28           NaN  NaN  NaN        NaN
===change===
0     2021/9/28 12:14:40 AM
1     2021/9/28 12:14:40 AM
2     2021/9/28 12:14:40 AM
3     2021/9/28 12:14:40 AM
47                2021/9/28
48                2021/9/28
49                2021/9/28
50    2021/9/28 01:50:23 PM
51    2021/9/28 01:50:23 PM
52    2021/9/28 01:20:12 PM
```
