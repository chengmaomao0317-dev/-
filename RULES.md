# 原渥集團活動成效統計 計算規則

## 檔案
`lalueur_campaign.html` 含兩個陣列：
- `RAW[]` — 課程活動記錄（無空格）
- `PROD_RAW[]` — 產品加購記錄（有空格）

---

## RAW 規則

### 基本欄位
```json
{"date":"YYYY-MM-DD","month":"YYYY-MM","activity":"活動名稱","act_price":數字,"client_type":"新客/舊客","client_name":"姓名","subtotal":數字,"ext_revenue":數字,"store":"門市","period":"20260901-20261031"}
```

### 美容九宮格（每次到店的第一筆）
- `subtotal` = 該次到店所有課程總金額
- `ext_revenue` = subtotal − act_price
- **必須是該客人當次到店的第一筆entry**

### 同一到店的第二課程（不同課程類型，如綺肌養護全臉升級）
- `subtotal` = act_price（與九宮格不同類型 → 獨立計算衍伸）
- `ext_revenue` = visit_total − act_price（同樣用到店總金額計算）

### 相同課程類型的追加堂數（如同一次又買多堂九宮格）
- `subtotal` = act_price
- `ext_revenue` = 0

### 升等體雕差額
- `subtotal` = act_price
- `ext_revenue` = 0

### 課程滿額贈
- ≥$15K → activity = `課程滿額贈$15000`
- ≥$35K → activity = `課程滿額贈$35000`
- `act_price` = 0
- `subtotal` = visit_total（到店課程總金額）
- `ext_revenue` = subtotal（same as subtotal）

### 贈品 / 舊客憑機票贈童顏1000發
- `act_price` = 0, `subtotal` = 0, `ext_revenue` = 0

---

## PROD_RAW 規則

### PROD_RAW 統一原則
**每個產品活動的第一筆（每種類型各自的第一筆）：**
- `subtotal` = 客人當次到店全部消費總金額（課程＋產品，與RAW中九宮格subtotal相同）
- `ext_revenue` = subtotal − 該活動類型總花費
  - 單件產品（P4、皮秒油、單筆599、單筆699）：ext = visit_total − act_price
  - 多件同類型（如699×4）：ext = visit_total − N×price

**同類型第二筆以後：**
- `subtotal` = act_price，`ext_revenue` = 0

### 週年慶產品滿額贈$5000 / $4000
- `act_price` = 0, `subtotal` = 0, `ext_revenue` = 0

---

## 門市名稱對照
| 收據寫法 | store 欄位 |
|---------|-----------|
| 館前 | `站前店` |
| 忠孝 | `忠孝店` |
| 板橋 | `板橋店` |
| 台中 | `台中店` |
| 大安 | `大安店` |
| 東門 | `東門店` |

## 活動名稱對照（收據寫法 → dashboard key）
| 收據 | Dashboard key |
|------|--------------|
| 人氣九宮格 / 美容九宮格 | `美容九宮格` |
| 綺肌養膚 / 綺肌養護 | `綺肌養護全臉升級` |
| 週年登機禮 | `舊客憑機票贈童顏1000發` |
| P4六盒 $3999 | `週年慶-P4六盒3999` |
| P4+三代膠囊+膠原蛋白 $1999 | `週年慶-P4組合1999` |

---

## 計算總原則
**衍伸業績 = 該客人當次消費總金額 − 該活動本身的費用**

每個有效活動（非贈品）都應有自己的衍伸業績計算，體現「因為這個活動，客人還額外花了多少」。
