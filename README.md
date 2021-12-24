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
