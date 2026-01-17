
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

--- 

# Angular - Vue

## 1. Diagram tổng thể: Angular mindset → Vue mindset

```
┌───────────────────────┐        ┌───────────────────────┐
│       Angular         │        │          Vue          │
│ (Framework nặng đô)   │        │ (Framework linh hoạt) │
└──────────┬────────────┘        └──────────┬────────────┘
           │                                   │
           │ Component Class                  │ Component Setup
           │ Decorators                       │ Composition API
           │ DI Container                     │ Explicit Imports
           │ RxJS everywhere                  │ reactivity (ref)
           ▼                                   ▼
   "Magic nhiều, rule chặt"          "Ít magic, code nói chuyện"
```

👉 Angular giống **quân đội chính quy**
👉 Vue giống **đội đặc nhiệm gọn nhẹ**


## 2. Lifecycle mapping (trọng tâm nhất)

### Diagram lifecycle: Angular → Vue

```
ANGULAR                         VUE 3 (Composition API)
────────────────────────────────────────────────────────
constructor()        ───────▶   setup()

ngOnInit()           ───────▶   onMounted()

ngOnChanges()        ───────▶   watch(props)

ngDoCheck()          ───────▶   watch / computed

ngAfterViewInit()    ───────▶   onMounted()

ngAfterViewChecked() ───────▶   watchEffect()

ngOnDestroy()        ───────▶   onBeforeUnmount()
                                   / onUnmounted()
```

👉 **Key insight**:
Vue **không ép lifecycle**, nó cho bạn **react với dữ liệu**, không react với framework.


## 3. Component structure mapping

### Angular Component

```
@Component({
  selector: 'app-user',
  templateUrl: './user.component.html'
})
export class UserComponent {
  @Input() user;
  @Output() save = new EventEmitter();
}
```

### Vue Component (tư duy tương đương)

```
<script setup>
const props = defineProps(['user'])
const emit = defineEmits(['save'])
</script>
```

### Diagram

```
Angular Class-based          Vue Function-based
──────────────────          ──────────────────
@Component                  <script setup>
@Input                       defineProps
@Output                      defineEmits
this.xxx                     ref / reactive
```

👉 Vue bỏ `this` → code **ít bug ngầm hơn**.



## 4. Data binding: template sang template

```
ANGULAR                     VUE
────────────────────────────────────────
{{ value }}          ↔     {{ value }}

[property]           ↔     :property

(event)              ↔     @event

[(ngModel)]          ↔     v-model
```

### Diagram binding

```
User Input
    ↓
v-model / ngModel
    ↓
Component State
```

👉 Vue `v-model` = **sugar syntax**, không có ControlValueAccessor đau đầu.



## 5. Dependency Injection vs Explicit Import

```
ANGULAR                         VUE
────────────────────────────────────────────────
constructor(private svc: Svc)   import svc

@NgModule providers              app.use(plugin)

DI container toàn cục             DI "bằng tay"
```

### Diagram

```
Angular:              Vue:
─────────             ─────────
Injector              Explicit import
   │                      │
   ▼                      ▼
Component            Component
```

👉 Vue chọn **rõ ràng hơn thông minh**.
Trade-off: bạn phải tự tổ chức code.



## 6. RxJS → Vue reactivity

```
ANGULAR                         VUE
────────────────────────────────────────────────
Observable                     ref / reactive

async pipe                     computed

subscribe()                    watch()

Subject                        ref + emit
```

### Diagram reactive

```
Data change
   ↓
ref / reactive
   ↓
Auto re-render
```

👉 Vue reactive = **pull-based**, không cần stream cho mọi thứ.



## 7. Routing

```
ANGULAR                         VUE
────────────────────────────────────────────────
@angular/router                vue-router

<router-outlet>                <router-view>

CanActivate                    beforeEach
```

Diagram:

```
URL
 ↓
Router
 ↓
Component
```



## 8. Mental model chuyển đổi (cái này là vàng)

```
Angular hỏi:
- Lifecycle nào?
- Inject gì?
- Observable đâu?

Vue hỏi:
- Data thay đổi ở đâu?
- Ai đang watch?
- Component này sống bao lâu?
```

👉 Angular = **framework điều khiển bạn**
👉 Vue = **bạn điều khiển framework**



## 9. Cheat-sheet mini (copy dán treo tường)

```
ngOnInit        → onMounted
ngOnDestroy     → onBeforeUnmount
@Input          → props
@Output         → emit
Service         → composableേഴ
Observable      → ref / watch
Module          → app.use()
```



## Lời khuyên của một thằng “đã đi cả hai phe”

Đừng cố viết Vue theo kiểu Angular.
Angular giỏi **enterprise discipline**.
Vue giỏi **tốc độ, độ mềm, khả năng tiến hóa**.

Hãy nghĩ Vue như:

> “Angular nhưng bạn phải tự chịu trách nhiệm về sự tỉnh táo của mình.”




