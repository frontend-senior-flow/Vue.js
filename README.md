
## 1. Tổng quan kiến trúc Vue.js (High-level)

```
┌──────────────┐
│   Browser    │
│  (User UI)   │
└──────┬───────┘
       │ Events (click, input)
       ▼
┌────────────────────┐
│   Vue Components   │
│  (Template + JS)   │
└──────┬─────────────┘
       │ Reactive Data
       ▼
┌────────────────────┐
│   Vue Reactivity   │
│ (ref, reactive)   │
└──────┬─────────────┘
       │ State Sync
       ▼
┌────────────────────┐
│   DOM Rendering    │
│ (Virtual DOM)     │
└────────────────────┘
```

👉 Tư tưởng cốt lõi: **User → Component → State → Render → User**
Vue sống nhờ vòng lặp này, rất “thiền”.


## 2. Diagram Component Tree (Cây component)

```
App.vue
 ├─ Header.vue
 │   ├─ Logo.vue
 │   └─ NavMenu.vue
 │       ├─ NavItem.vue
 │       └─ NavItem.vue
 │
 ├─ RouterView
 │   ├─ HomePage.vue
 │   │   ├─ Banner.vue
 │   │   └─ ProductList.vue
 │   │       └─ ProductItem.vue
 │   │
 │   └─ DetailPage.vue
 │       └─ CommentBox.vue
 │
 └─ Footer.vue
```

👉 Nhìn cái cây này là debug **props / emit / slot** nhanh hơn uống cà phê.

## 3. Data Flow: Props down – Events up (chuẩn Vue)

```
Parent Component
     │
     │  props
     ▼
Child Component
     │
     │  emit(event)
     ▼
Parent Component
```

Ví dụ đời thường:

```
TodoList.vue
   │  :todos
   ▼
TodoItem.vue
   │  emit("remove")
   ▼
TodoList.vue
```

👉 Luật cổ điển nhưng bền vững: **cha cho dữ liệu – con báo kết quả**.

---

## 4. Vue + Pinia (State Management)

```
┌──────────────┐
│   Component  │
│  (View)     │
└──────┬───────┘
       │ useStore()
       ▼
┌──────────────────┐
│   Pinia Store    │
│ state / getters │
│ actions         │
└──────┬───────────┘
       │ API call
       ▼
┌──────────────────┐
│   Backend API    │
└──────────────────┘
```

👉 Component **không giữ bí mật**, mọi drama đều đưa vào store.


## 5. Lifecycle Diagram (vòng đời component)

```
beforeCreate
     ↓
created
     ↓
beforeMount
     ↓
mounted
     ↓
──────────────
  component
   running
──────────────
     ↓
beforeUpdate
     ↓
updated
     ↓
beforeUnmount
     ↓
unmounted
```

👉 `mounted()` là nơi yêu thích của dân gọi API.
👉 `beforeUnmount()` là chỗ dọn dẹp, không dọn là leak như nước tràn bờ.


## 6. Vue Router Flow

```
User clicks link
      ↓
Vue Router
      ↓
Check route
      ↓
Load component
      ↓
Render <router-view>
```

Có guard thì thêm gia vị:

```
beforeEach
   ↓
auth check
   ↓
allow / redirect
```


