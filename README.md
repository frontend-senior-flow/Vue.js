# 1. Vue diagram

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

# 2. Vue Lifecycle

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

# 3. Vue state management


## 1. Big picture: Angular State vs Vue State

```
ANGULAR (enterprise-style)          VUE (pragmatic-style)
──────────────────────────          ─────────────────────
Service singleton                   Store (Pinia)
RxJS Observable                     reactive / ref
NgRx (Redux pattern)                Pinia (lighter)
Async pipe                          computed / watch
Facade pattern                      composable
```

👉 Angular: **state = stream**
👉 Vue: **state = dữ liệu sống**


## 2. Diagram tổng quát: State flow

### Angular (Service / NgRx)

```
Component
   │ dispatch / subscribe
   ▼
Store / Service
   │ Observable stream
   ▼
Reducer / Effect
   │ HTTP
   ▼
Backend API
```

### Vue (Pinia)

```
Component
   │ useStore()
   ▼
Pinia Store
   │ action()
   ▼
Backend API
```

👉 Vue cắt bớt 2–3 tầng nghi lễ. Ít họp hành, làm việc luôn.



## 3. Angular Service ↔ Vue Store (mapping trực tiếp)

### Angular Service (state đơn giản)

```
@Injectable({ providedIn: 'root' })
export class UserService {
  private user$ = new BehaviorSubject<User>(null);

  setUser(u: User) {
    this.user$.next(u);
  }

  getUser() {
    return this.user$.asObservable();
  }
}
```

### Vue Pinia Store (tư duy tương đương)

```
state: user
actions: setUser
getters: user
```

### Diagram mapping

```
BehaviorSubject     → state (reactive)
.next()             → action()
.asObservable()     → store.user
```

👉 Vue không cần stream nếu không có async phức tạp.



## 4. NgRx ↔ Pinia (so sánh thẳng mặt)

### Angular NgRx

```
Component
   │ dispatch(Action)
   ▼
Reducer
   │ new State
   ▼
Store
   │ select()
   ▼
Component
```

### Vue Pinia

```
Component
   │ store.action()
   ▼
Store
   │ reactive state
   ▼
Component (auto update)
```

👉 Pinia = **NgRx đã bỏ bớt lễ nghi**.



## 5. Keyword mapping table (rất quan trọng)

```
ANGULAR (NgRx)          VUE (Pinia)
────────────────────────────────────
Action                 action (function)
Reducer                implicit (inside action)
Selector               getter / computed
Effect                 async action
StoreModule             app.use(pinia)
```

👉 Vue **không tách reducer** → logic đọc được bằng mắt người.



## 6. State scope (điểm Angular dev hay nhầm)

### Angular

```
Component state   → class property
Shared state      → Service / Store
Global state      → NgRx
```

### Vue

```
Component state   → ref / reactive
Shared state      → composable
Global state      → Pinia
```

### Diagram scope

```
Local ──▶ ref()
Shared ─▶ useXxx()
Global ─▶ useXxxStore()
```

👉 Vue cho bạn **nhiều tầng state nhẹ**, không ép tất cả vào global.



## 7. RxJS → Vue reactivity (chuyển não)

```
ANGULAR                    VUE
────────────────────────────────────────
combineLatest               computed
switchMap                   watch
takeUntil(destroy$)         onBeforeUnmount
Subject                     ref + emit
```

### Diagram reactive

```
state change
   ↓
reactive system
   ↓
component re-render
```

👉 Vue reactive = **push-based + auto dependency tracking**
Không cần nhớ unsubscribe nếu dùng đúng API.


## 8. Khi nào Angular-style, khi nào Vue-style?

**Angular dev mới sang Vue hay mắc lỗi:**

* Nhồi mọi thứ vào store như NgRx ❌
* Dùng watch thay cho computed ❌
* Tạo “service class” y hệt Angular ❌

**Vue style đúng:**

```
UI logic        → component
Business logic → composable
Shared state   → Pinia
```


## 9. Cheat-sheet treo tường (state management)

```
Angular Service      → Vue composable
NgRx Store           → Pinia Store
Observable           → ref / computed
Selector             → getter
Effect               → async action
```



## Một câu chốt cho người từng chinh chiến Angular

Angular hỏi:

> “State này thuộc module nào?”

Vue hỏi:

> “State này có cần share không?”

Vue không chống enterprise.
Vue chống **over-engineering**.



---

# 4. Vue Router – Lazy loading – Form



## 1. ROUTING (Angular → Vue)

### Keyword mapping

```
ANGULAR                         VUE
────────────────────────────────────────
@angular/router                vue-router
Routes[]                        routes[]
RouterModule.forRoot()          createRouter()
<router-outlet>                <router-view>
routerLink                     <router-link>
CanActivate                    beforeEach / beforeEnter
```

### Diagram: Routing flow

#### Angular

```
URL change
   ↓
Router
   ↓
CanActivate / Resolver
   ↓
Component
```

#### Vue

```
URL change
   ↓
vue-router
   ↓
beforeEach / beforeEnter
   ↓
Component
```

👉 Vue **không có Resolver riêng** – logic thường nằm trong `setup()` hoặc `beforeEnter`.


### Mental shift

```
Angular: Route là cấu hình + vòng đời riêng
Vue: Route là dữ liệu + hook
```

## 2. LAZY LOADING (điểm giống nhau nhất)

### Keyword mapping

```
ANGULAR                         VUE
────────────────────────────────────────
loadChildren                   () => import()
NgModule lazy                  Component lazy
PreloadingStrategy             dynamic import
```

### Diagram: Lazy loading

#### Angular

```
Navigate to /admin
    ↓
Load admin.module.ts
    ↓
Load components
```

#### Vue

```
Navigate to /admin
    ↓
import('Admin.vue')
    ↓
Render component
```


### Vue route lazy example (tư duy)

```
/admin
  └─ import Admin.vue khi cần
```

👉 Vue lazy **nhẹ hơn** vì không có module layer.


### Mental shift

```
Angular lazy-load module
Vue lazy-load component
```


## 3. FORM (chỗ Angular dev hay… đau đầu nhất)

### Keyword mapping tổng quát

```
ANGULAR                         VUE
────────────────────────────────────────
Template-driven form            v-model
ReactiveForm                    reactive + ref
FormControl                     ref
FormGroup                       reactive object
Validator                       custom function
ControlValueAccessor            v-model
```


### 3.1 Template-driven Form → v-model

#### Diagram

```
Input
  ↓
ngModel / v-model
  ↓
Component State
```

👉 Vue `v-model` = **2-way binding thuần**, không cần directive phức tạp.


### 3.2 Reactive Form → reactive state

#### Angular Reactive Form

```
FormGroup
 ├─ FormControl
 ├─ FormControl
 └─ Validator
```

#### Vue tương đương

```
reactive({
  field1,
  field2,
  errors
})
```

#### Diagram

```
Input
  ↓
v-model
  ↓
reactive state
  ↓
validate()
```

### 3.3 Validation mapping

```
ANGULAR                         VUE
────────────────────────────────────────
Validators.required             custom fn
Validators.pattern              regex check
statusChanges                   watch()
valueChanges                    watch()
```

#### Diagram validate

```
value change
   ↓
watch()
   ↓
set error state
```

👉 Vue **không ép chuẩn validation**, bạn chọn lib (VeeValidate, Yup) hoặc tự viết.


## 4. Guard / Auth / Permission

### Mapping

```
ANGULAR                         VUE
────────────────────────────────────────
CanActivate                    beforeEach
CanDeactivate                  beforeRouteLeave
Resolve                        fetch in setup()
```

#### Diagram auth

```
Route enter
   ↓
check auth
   ↓
allow / redirect
```


## 5. Tổng hợp “dịch não” Angular → Vue

```
Angular hỏi:
- Module đâu?
- Guard nào?
- FormControl nào?

Vue hỏi:
- Component này load khi nào?
- State này reactive chưa?
- Có cần share không?
```


## 6. Master cheat-sheet (in ra dán tường)

```
ROUTER
router-outlet        → router-view
CanActivate          → beforeEach

LAZY
loadChildren         → () => import()

FORM
FormGroup            → reactive({})
FormControl          → ref
ReactiveForm         → reactive + watch
Validator            → custom fn
```


# 5. Composable · Reactivity · Computed/Watch · Slot · Provide/Inject · Directive · SSR · Error Handling · Performance

## 1. Composable (khái niệm SỐ 1 Angular dev hay thiếu)

### Mapping

```
ANGULAR                    VUE
────────────────────────────────────
Service (logic)            composable
Facade pattern             composable
Helper + DI                composable
```

### Diagram

```
Component
   │ useUser()
   ▼
Composable
   │ state + logic
   ▼
API / utils
```

👉 Composable = **service nhưng không có DI container**
👉 Đây là “linh hồn” của Vue 3


## 2. Reactivity system (Vue không phải Angular lite)

### Angular

```
Change Detection
   ↓
Zone.js
   ↓
Check whole tree
```

### Vue

```
Dependency tracking
   ↓
reactive / ref
   ↓
Update đúng chỗ
```

### Diagram

```
read state ─▶ tracked
write state ─▶ re-render đúng component
```

👉 Vue **không dirty-check**
👉 Đọc ở đâu, update ở đó



## 3. Computed vs Watch (Angular dev hay dùng sai)

### Mapping

```
ANGULAR                    VUE
────────────────────────────────────
Getter + memo              computed
ngOnChanges                watch
```

### Diagram

```
state ─▶ computed ─▶ UI
state ─▶ watch ─▶ side-effect
```

👉 Rule vàng:

* `computed` → **tính toán**
* `watch` → **phản ứng phụ** (API, log, sync)



## 4. Slots (người anh em của Content Projection)

### Mapping

```
ANGULAR                    VUE
────────────────────────────────────
<ng-content>               <slot>
select                      named slot
TemplateRef                scoped slot
```

### Diagram

```
Parent
  └─ content
       ↓
     Slot
```

👉 Slot của Vue **mạnh hơn** content projection nếu dùng đúng.



## 5. Provide / Inject (DI nhưng “thiền” hơn)

### Mapping

```
ANGULAR                    VUE
────────────────────────────────────
Injector                    provide / inject
Global service              app.provide()
```

### Diagram

```
Ancestor
  provide
    ↓
Descendant
  inject
```

👉 Dùng cho **theme, i18n, auth context**, không phải cho mọi service.



## 6. Directives (đừng nghĩ Vue thiếu)

### Mapping

```
ANGULAR                    VUE
────────────────────────────────────
@Directive                 v-directive
HostListener               directive hooks
```

### Diagram

```
Element
   ↓
Directive
   ↓
DOM behavior
```

👉 Vue directive = **DOM-level logic**, không phải business logic.



## 7. Render flow & Virtual DOM

### Angular

```
Template
  ↓
Change Detection
  ↓
DOM update
```

### Vue

```
Template
  ↓
Virtual DOM
  ↓
Patch minimal changes
```

👉 Vue tối ưu bằng **dependency**, không phải vòng quét.



## 8. SSR / Hydration (nếu làm web lớn)

### Mapping

```
ANGULAR                    VUE
────────────────────────────────────
Angular Universal           Nuxt
SSR module                  server renderer
```

### Diagram

```
Server render
   ↓
HTML
   ↓
Hydration
   ↓
Client app
```



## 9. Error handling

### Mapping

```
ANGULAR                    VUE
────────────────────────────────────
ErrorHandler                errorCaptured
try/catch RxJS              try/catch async
```

### Diagram

```
Child error
   ↓
errorCaptured
   ↓
fallback UI
```



## 10. Performance mindset (rất khác Angular)

```
ANGULAR tối ưu bằng:
- OnPush
- trackBy
- manual unsubscribe

VUE tối ưu bằng:
- computed
- shallowReactive
- split component
```

👉 Vue performance = **thiết kế reactive đúng**, không phải tweak flag.




