# Options: State {#options-state}

## data {#data}

A function that returns the initial reactive state for the component instance.

- **Type**

  ```ts
  interface ComponentOptions {
    data?(
      this: ComponentPublicInstance,
      vm: ComponentPublicInstance
    ): object
  }
  ```

- **Details**

از تابع انتظار می رود یک شی ساده جاوااسکریپت برگرداند که توسط Vue واکنش‌پذیر می‌شود. پس از ایجاد نمونه، می‌توان به شی داده‌های واکنش‌پذیر به عنوان `this.$data` دسترسی پیدا کرد. نمونه کامپوننت همچنین همه خصوصیات یافت شده در شی داده‌ها را واسطه‌گری می‌کند، به طوری که `this.a` معادل `this.$data.a` خواهد بود.

تمام خصوصیات داده‌های سطح بالا باید در شی داده‌های برگردانده شده شامل شوند. افزودن خصوصیات جدید به `this.$data` امکان‌پذیر است، اما **توصیه نمی‌شود**. اگر مقدار مورد نظر یک خاصیت هنوز در دسترس نیست، باید یک مقدار خالی مانند `undefined` یا `null` به عنوان جانگهدار شامل شود تا Vue بداند این خاصیت وجود دارد.

خصوصیاتی که با `_` یا `$` شروع می‌شوند **واسطه‌گری نخواهند شد** زیرا ممکن است با خصوصیات داخلی و روش‌های API  Vue تداخل داشته باشند. برای دسترسی به آن‌ها باید از `this.$data._property` استفاده کرد.

**توصیه نمی‌شود** شی‌هایی با رفتار حالت‌دار خودشان مانند شی‌های API مرورگر و خصوصیات پروتوتایپ برگردانده شوند. شی برگردانده شده ایده‌آل‌تر است که یک شی ساده باشد که فقط حالت کامپوننت را نمایش می‌دهد.

- **Example**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    created() {
      console.log(this.a) // 1
      console.log(this.$data) // { a: 1 }
    }
  }
  ```

  توجه داشته باشید اگر از تابع پیکانی با پراپرتی `data` استفاده کنید، `this` اشاره به نمونه کامپوننت نخواهد داشت، اما همچنان می‌توانید به عنوان اولین آرگومان تابع به نمونه دسترسی داشته باشید:

  ```js
  data: (vm) => ({ a: vm.myProp })
  ```

- **See also** [Reactivity in Depth](/guide/extras/reactivity-in-depth)

## props {#props}

پراپ‌های یک کامپوننت را تعریف می‌کند.

- **Type**

  ```ts
  interface ComponentOptions {
    props?: ArrayPropsOptions | ObjectPropsOptions
  }

  type ArrayPropsOptions = string[]

  type ObjectPropsOptions = { [key: string]: Prop }

  type Prop<T = any> = PropOptions<T> | PropType<T> | null

  interface PropOptions<T> {
    type?: PropType<T>
    required?: boolean
    default?: T | ((rawProps: object) => T)
    validator?: (value: unknown) => boolean
  }

  type PropType<T> = { new (): T } | { new (): T }[]
  ```

  > نوع داد‌ها برای خوانایی بیشتر خلاصه شده‌اند.

- **Details**

  در Vue، تمام propهای کامپوننت باید به طور صریح اعلام شوند. Propهای کامپوننت می‌توانند به دو شکل اعلام شوند:

  - شکل ساده با استفاده از آرایه‌ای از رشته
  - شکل کامل با استفاده از یک شی که هر کلید خاصیت آن نام prop است، و مقدار آن نوع prop (تابع سازنده) یا گزینه‌های پیشرفته است.

با سینتکس مبتنی بر شی، هر prop می‌تواند گزینه‌های زیر را مشخص کند:

  - **`type`**:
می‌تواند یکی از این نوع‌داده‌های بومی باشد: `String`، `Number`، `Boolean`، `Array`، `Object`، `Date`، `Function`، `Symbol` و یا هر تابع سازنده سفارشی یا آرایه‌ای از آن‌ها. در حالت  development، Vue بررسی می‌کند آیا مقدار یک prop با نوع اعلام شده مطابقت دارد یا خیر، و اگر مطابقت نداشته باشد هشداری را نمایش می‌دهد. برای جزئیات بیشتر به [Prop Validation](/guide/components/props#prop-validation) مراجعه کنید.

    همچنین توجه داشته باشید که یک prop با نوع `Boolean` بر روی رفتار casting آن در هر دو حالت development و production تأثیر می‌گذارد. برای جزئیات بیشتر به [Boolean Casting](/guide/components/props#boolean-casting) مراجعه کنید.

  - **`default`**:
مقدار پیش‌فرضی برای prop را هنگامی که توسط والد پاس داده نشده یا مقدار `undefined` دارد، مشخص می‌کند. پیش‌فرض‌های شی یا آرایه باید با استفاده از تابع فکتوری برگردانده شوند. تابع فکتوری همچنین شی خام props را به عنوان آرگومان دریافت می‌کند.

  - **`required`**:
تعریف می‌کند آیا prop الزامی است یا خیر. در محیط غیر تولیدی، اگر این مقدار درست باشد و prop پاس داده نشود، هشداری در کنسول نمایش داده خواهد شد.

  - **`validator`**:
تابع اعتبارسنجی سفارشی که تنها آرگومان آن مقدار prop است. در حالت development، اگر این تابع یک مقدار نادرست (یعنی اعتبارسنجی ناموفق باشد) برگرداند، هشداری در کنسول نمایش داده خواهد شد.

- **مثال**

  تعریف ساده:

  ```js
  export default {
    props: ['size', 'myMessage']
  }
  ```

  تعریف شی با اعتبارسنجی:

  ```js
  export default {
    props: {
      // type check
      height: Number,
      // type check plus other validations
      age: {
        type: Number,
        default: 0,
        required: true,
        validator: (value) => {
          return value >= 0
        }
      }
    }
  }
  ```

- **See also**
  - [Guide - Props](/guide/components/props)
  - [Guide - Typing Component Props](/guide/typescript/options-api#typing-component-props) <sup class="vt-badge ts" />

## computed {#computed}

Declare computed properties to be exposed on the component instance.

- **Type**

  ```ts
  interface ComponentOptions {
    computed?: {
      [key: string]: ComputedGetter<any> | WritableComputedOptions<any>
    }
  }

  type ComputedGetter<T> = (
    this: ComponentPublicInstance,
    vm: ComponentPublicInstance
  ) => T

  type ComputedSetter<T> = (
    this: ComponentPublicInstance,
    value: T
  ) => void

  type WritableComputedOptions<T> = {
    get: ComputedGetter<T>
    set: ComputedSetter<T>
  }
  ```

- **جزئیات**

  The option accepts an object where the key is the name of the computed property, and the value is either a computed getter, or an object with `get` and `set` methods (for writable computed properties).

  All getters and setters have their `this` context automatically bound to the component instance.

  Note that if you use an arrow function with a computed property, `this` won't point to the component's instance, but you can still access the instance as the function's first argument:

  ```js
  export default {
    computed: {
      aDouble: (vm) => vm.a * 2
    }
  }
  ```

- **Example**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    computed: {
      // readonly
      aDouble() {
        return this.a * 2
      },
      // writable
      aPlus: {
        get() {
          return this.a + 1
        },
        set(v) {
          this.a = v - 1
        }
      }
    },
    created() {
      console.log(this.aDouble) // => 2
      console.log(this.aPlus) // => 2

      this.aPlus = 3
      console.log(this.a) // => 2
      console.log(this.aDouble) // => 4
    }
  }
  ```

- **See also**
  - [Guide - Computed Properties](/guide/essentials/computed)
  - [Guide - Typing Computed Properties](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

## methods {#methods}

Declare methods to be mixed into the component instance.

- **Type**

  ```ts
  interface ComponentOptions {
    methods?: {
      [key: string]: (this: ComponentPublicInstance, ...args: any[]) => any
    }
  }
  ```

- **Details**

  Declared methods can be directly accessed on the component instance, or used in template expressions. All methods have their `this` context automatically bound to the component instance, even when passed around.

  Avoid using arrow functions when declaring methods, as they will not have access to the component instance via `this`.

- **Example**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    methods: {
      plus() {
        this.a++
      }
    },
    created() {
      this.plus()
      console.log(this.a) // => 2
    }
  }
  ```

- **See also** [Event Handling](/guide/essentials/event-handling)

## watch {#watch}

Declare watch callbacks to be invoked on data change.

- **Type**

  ```ts
  interface ComponentOptions {
    watch?: {
      [key: string]: WatchOptionItem | WatchOptionItem[]
    }
  }

  type WatchOptionItem = string | WatchCallback | ObjectWatchOptionItem

  type WatchCallback<T> = (
    value: T,
    oldValue: T,
    onCleanup: (cleanupFn: () => void) => void
  ) => void

  type ObjectWatchOptionItem = {
    handler: WatchCallback | string
    immediate?: boolean // default: false
    deep?: boolean // default: false
    flush?: 'pre' | 'post' | 'sync' // default: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }
  ```

  > Types are simplified for readability.

- **Details**

  The `watch` option expects an object where keys are the reactive component instance properties to watch (e.g. properties declared via `data` or `computed`) — and values are the corresponding callbacks. The callback receives the new value and the old value of the watched source.

  In addition to a root-level property, the key can also be a simple dot-delimited path, e.g. `a.b.c`. Note that this usage does **not** support complex expressions - only dot-delimited paths are supported. If you need to watch complex data sources, use the imperative [`$watch()`](/api/component-instance#watch) API instead.

  The value can also be a string of a method name (declared via `methods`), or an object that contains additional options. When using the object syntax, the callback should be declared under the `handler` field. Additional options include:

  - **`immediate`**: trigger the callback immediately on watcher creation. Old value will be `undefined` on the first call.
  - **`deep`**: force deep traversal of the source if it is an object or an array, so that the callback fires on deep mutations. See [Deep Watchers](/guide/essentials/watchers#deep-watchers).
  - **`flush`**: adjust the callback's flush timing. See [Callback Flush Timing](/guide/essentials/watchers#callback-flush-timing) and [`watchEffect()`](/api/reactivity-core#watcheffect).
  - **`onTrack / onTrigger`**: debug the watcher's dependencies. See [Watcher Debugging](/guide/extras/reactivity-in-depth#watcher-debugging).

  Avoid using arrow functions when declaring watch callbacks as they will not have access to the component instance via `this`.

- **Example**

  ```js
  export default {
    data() {
      return {
        a: 1,
        b: 2,
        c: {
          d: 4
        },
        e: 5,
        f: 6
      }
    },
    watch: {
      // watching top-level property
      a(val, oldVal) {
        console.log(`new: ${val}, old: ${oldVal}`)
      },
      // string method name
      b: 'someMethod',
      // the callback will be called whenever any of the watched object properties change regardless of their nested depth
      c: {
        handler(val, oldVal) {
          console.log('c changed')
        },
        deep: true
      },
      // watching a single nested property:
      'c.d': function (val, oldVal) {
        // do something
      },
      // the callback will be called immediately after the start of the observation
      e: {
        handler(val, oldVal) {
          console.log('e changed')
        },
        immediate: true
      },
      // you can pass array of callbacks, they will be called one-by-one
      f: [
        'handle1',
        function handle2(val, oldVal) {
          console.log('handle2 triggered')
        },
        {
          handler: function handle3(val, oldVal) {
            console.log('handle3 triggered')
          }
          /* ... */
        }
      ]
    },
    methods: {
      someMethod() {
        console.log('b changed')
      },
      handle1() {
        console.log('handle 1 triggered')
      }
    },
    created() {
      this.a = 3 // => new: 3, old: 1
    }
  }
  ```

- **See also** [Watchers](/guide/essentials/watchers)

## emits {#emits}

Declare the custom events emitted by the component.

- **Type**

  ```ts
  interface ComponentOptions {
    emits?: ArrayEmitsOptions | ObjectEmitsOptions
  }

  type ArrayEmitsOptions = string[]

  type ObjectEmitsOptions = { [key: string]: EmitValidator | null }

  type EmitValidator = (...args: unknown[]) => boolean
  ```

- **Details**

  Emitted events can be declared in two forms:

  - Simple form using an array of strings
  - Full form using an object where each property key is the name of the event, and the value is either `null` or a validator function.

  The validation function will receive the additional arguments passed to the component's `$emit` call. For example, if `this.$emit('foo', 1)` is called, the corresponding validator for `foo` will receive the argument `1`. The validator function should return a boolean to indicate whether the event arguments are valid.

  Note that the `emits` option affects which event listeners are considered component event listeners, rather than native DOM event listeners. The listeners for declared events will be removed from the component's `$attrs` object, so they will not be passed through to the component's root element. See [Fallthrough Attributes](/guide/components/attrs) for more details.

- **Example**

  Array syntax:

  ```js
  export default {
    emits: ['check'],
    created() {
      this.$emit('check')
    }
  }
  ```

  Object syntax:

  ```js
  export default {
    emits: {
      // no validation
      click: null,

      // with validation
      submit: (payload) => {
        if (payload.email && payload.password) {
          return true
        } else {
          console.warn(`Invalid submit event payload!`)
          return false
        }
      }
    }
  }
  ```

- **See also**
  - [Guide - Fallthrough Attributes](/guide/components/attrs)
  - [Guide - Typing Component Emits](/guide/typescript/options-api#typing-component-emits) <sup class="vt-badge ts" />

## expose {#expose}

Declare exposed public properties when the component instance is accessed by a parent via template refs.

- **Type**

  ```ts
  interface ComponentOptions {
    expose?: string[]
  }
  ```

- **Details**

  By default, a component instance exposes all instance properties to the parent when accessed via `$parent`, `$root`, or template refs. This can be undesirable, since a component most likely has internal state or methods that should be kept private to avoid tight coupling.

  The `expose` option expects a list of property name strings. When `expose` is used, only the properties explicitly listed will be exposed on the component's public instance.

  `expose` only affects user-defined properties - it does not filter out built-in component instance properties.

- **Example**

  ```js
  export default {
    // only `publicMethod` will be available on the public instance
    expose: ['publicMethod'],
    methods: {
      publicMethod() {
        // ...
      },
      privateMethod() {
        // ...
      }
    }
  }
  ```
