---
sidebarDepth: 2
---

# 基本配置

## 参数 Props

### schema
* required：`true`
* 类型：`object`
* 默认值：`undefined`

用于描述表单数据的 JSON Schema，
遵循 [JSON Schema](https://json-schema.org/understanding-json-schema/index.html) 规范

```
title: '渲染为标题',
description: '渲染为描述信息' // 支持html代码
```

这里如果是 `object` 或者`array` 内的 `title` `description` 会被渲染为包裹容器`FieldGroupWrap` 标题和描述。

内部的 `title` `description` 会被 `widget` 组件渲染为 `formItem` 的标题和描述

:::tip 如何隐藏
* 不配置 `title` `description` 属性不会显示
* 特例：对于`object` `array` 类型可以通过 [ui-schema showTitle](#ui-schema) 参数控制是否显示
:::


**例: 配置用户信息表单**
::: demo
```html
<template>
    <vue-form
        v-model="formData"
        :schema="schema"
    >
    </vue-form>
</template>

<script>
export default {
    name: 'Demo',
    data() {
        return {
            formData: {},
            schema: {
                title: 'UserInfo 表单',
                description: 'A simple form example.',
                type: 'object',
                required: [
                    'firstName',
                    'lastName'
                ],
                properties: {
                    firstName: {
                        type: 'string',
                        title: 'First name',
                        default: 'Jun'
                    },
                    lastName: {
                        type: 'string',
                        title: 'Last name'
                    },
                    age: {
                        type: 'integer',
                        title: 'Age',
                        maximum: 80,
                        minimum: 16
                    },
                    bio: {
                        type: 'string',
                        title: 'Bio',
                        minLength: 10
                    },
                    password: {
                        type: 'string',
                        title: 'Password',
                        minLength: 3
                    },
                    telephone: {
                        type: 'string',
                        title: 'Telephone',
                        minLength: 10
                    }
                }
            }
        };
    }
};
</script>
```
:::

### ui-schema
* 类型：`object`
* 默认值：`{}`

>* `0.0.16` 之后版本支持配置 `ui-schema` 在 `schema` 参数中 [点击查看](#ui-schema配置在schema中)
>* `0.1.0` 之后版本支持配置 [error-schema](#error-schema) 在 [ui-schema](#ui-schema) 中。（`ui-schema` 和 `error-schema` 格式完全相同，且同属ui显示，一份可方便配置）

用于配置表单展示样式，普通json数据，非 `JSON Schema` 规范

:::tip
* 配置数据结构和 `schema` 保持一致，所有的ui配置属性 `ui:` 开头
* 也可以在 `ui:options` 内配置所有的属性，不需要 `ui:` 开头
* 如果配置了`ui:xx` 和 `ui:options` 内配置了`xx`属性，`ui:options`内的优先级更高，实际上你可以把所有的参数都配置在 `ui:options` 内；这里可以按照个人习惯，推荐使用如下参数格式
> 注：ui-schema 为普通json数据，并非 JSON Schema 规范语法
:::

:::warning 注意
* `ui:widget` `ui:field` `ui:fieldProps` 不支持配置在 `ui:options` 中
:::

通用参数格式如下：
```js
uiSchema = {
    'ui:title': '覆盖schema title', // 覆盖schema title
    'ui:description': '覆盖schema description描述信息',  // 覆盖schema description
    'ui:emptyValue': undefined,   // 表单元素输入为空时的值，默认 undefined
    'ui:field': 'componentName', // 自定义field，不支持配置在options中
    'ui:fieldProps': undefined, // 传给field的参数，自定义field可以使用，props: { fieldProps }
    'ui:widget': 'el-slider', // 配置input组件，支持字符串或者传入一个vue组件，不支持配置在options中
    'ui:labelWidth': '50px', // form item label宽度
    'ui:options': {
            showTitle: '是否显示标题', // 只对 type为`object`、`array` 类型有效
            showDescription: '是否显示描述信息', // 只对type为 `object`、`array` 类型有效
            attrs: {
                // 通过 vue render函数 attrs 传递给 Widget 组件，只能配置在叶子节点
                // 你也配置在外层，程序会合并 attrs 和 其它外层属性 通过 attrs 传递给子组件
                autofocus: true
            },
            style: {
                // 通过 vue render函数 style 传递给 Widget 组件，只能配置在叶子节点
                boxShadow: '0 0 6px 2px #2b9939'
            },
            class: {
                // 0.1.0 版本添加
                // 通过 vue render函数 class 传递给 Widget 组件，只能配置在叶子节点
                className_hei: true
            },
            fieldStyle: {
                // 0.1.0 版本添加
                // 通过 vue render函数 style 传递给 Field 组件，支持所有field节点
                background: 'red'
            },
            fieldClass: {
                // 0.1.0 版本添加
                // 通过 vue render函数 class 传递给 Field 组件，支持所有field节点
                fieldClass: true
            },
            fieldAttrs: {
                // 通过 vue render函数 attrs 传递给 Field 组件，支持所有field节点
                'attr-x': 'xxx'
            },

            // 其它所有参数会通过 props 传递给 Widget 组件
            type: 'textarea',
            placeholder: '请输入你的内容'
    }
}
```

>1. `ui:field` 自定义field组件参见这里  [自定义 field](/zh/guide/adv-config.html#自定义-field)
>1. `ui:widget` 自定义widget组件参见这里  [自定义 widget](/zh/guide/adv-config.html#自定义-widget)


#### ui-schema配置在schema中

`0.0.16` 之后版本，`ui-schema` 所有配置都支持直接配置在 `schema` 参数中

* `ui-schema` 单独配置优先级大于 `schema` 中配置
* 好处可以一份配置，不过会使 `schema` 不再是一份纯粹的 `JSON Schema` 文件，结合实际场景选择方案。

如下格式：
```json
{
    "title": "Demo",
    "type": "object",
    "ui:order": [
        "bio",
        "firstName"
    ],
    "properties": {
        "firstName": {
            "type": "string",
            "title": "First name",
            "ui:placeholder": "请输入FirstName（配置在schema中）"
        },
        "bio": {
            "type": "string",
            "title": "Bio",
            "minLength": 10,
            "ui:options": {
                "type": "textarea",
                "placeholder": "请输入FirstName（配置在schema中）",
                "rows": 4
            }
        }
    }
}
```

#### ui-schema配置演示：重置表单widget样式
::: demo
```html
<template>
    <vue-form
        v-model="formData"
        :ui-schema="uiSchema"
        :schema="schema"
    >
    </vue-form>
</template>

<script>
export default {
    name: 'Demo',
    data() {
        return {
            formData: {
                number: 1,
                numberEnumRadio: 2,
                integerRange: 50,
            },
            schema: {
                type: 'object',
                properties: {
                    firstName: {
                        type: 'string',
                        title: 'First name',
                        'ui:placeholder': '请输入FirstName（配置在schema中）'
                    },
                    bio: {
                        type: 'string',
                        title: 'Bio',
                        minLength: 10,
                        'ui:options': {
                            type: 'textarea',
                            placeholder: 'placeholder（配置在schema中）',
                            rows: 4
                        }
                    },
                    inputText: {
                        title: 'Input Text',
                        type: 'string'
                    },
                    number: {
                        title: 'Number (默认渲染组件)',
                        type: 'number'
                    },
                    integerRange: {
                        title: 'Integer range (使用 ElSlider)',
                        type: 'integer',
                        minimum: 42,
                        maximum: 100
                    }
                }
            },
            uiSchema: {
                'ui:order': ['number', '*'],
                inputText: {
                    'ui:description': '这里重置了描述信息',
                    'ui:emptyValue': '',
                    'ui:options': {
                        style: {
                            boxShadow: '0 0 6px 2px #2b9939'
                        },
                        class: {
                            className_hei: true
                        },
                        type: 'textarea',
                        autofocus: true,
                        rows: 6,
                        placeholder: '请输入你的内容'
                    }
                },
                number: {
                    'ui:title': '这里重置了标题'
                },
                integerRange: {
                    'ui:widget': 'el-slider',
                }
            }
        }
    }
};
</script>
```
:::

::: warning 注意
配置数据结构是和 `schema` 保持一致，而非 `formData` 一致

比如配置数组元素：
```js

// schema
schema = {
    type: 'object',
    properties: {
        fixedItemsList: {
            type: 'array',
            title: 'A list of fixed items',
            items: [
                {
                    title: 'A string value',
                    type: 'string',
                    maxLength: 2
                }
            ]
        }
    }
}
```

```js
// 正确的配置
uiSchema = {
    fixedItemsList: {
         // 这里保持和 schema 结构相同
         items: [
             {
                 'ui:options': {
                    ...
                }
             }
         ]
    }
}
```

```js
// 错误的配置
uiSchema = {
    fixedItemsList: [
         {
             'ui:options': {
                ...
            }
        }
    ]
}
```

:::

### error-schema
* 类型：`object`
* 默认值：`{}`

>* `0.0.16` 之后版本支持配置 `error-schema` 在 `schema` 参数中 [点击查看](#error-schema配置在schema中)
>* `0.1.0` 之后版本支持配置 [error-schema](#error-schema) 在 [ui-schema](#ui-schema) 中。（`ui-schema` 和 `error-schema` 格式完全相同，且同属ui显示，一份可方便配置）

用于配置表单校验错误文案信息，普通json数据，非 JSON Schema 规范

数据配置和 `ui-schema` 保存一致，区别在于：
1. 使用 `err:` 做前缀
1. 使用配置的 schema 错误类型的 `err:${name}` 做key，比如 `err:format` 、`err:required` 、`err:type`

::: tip
 * 配置数据结构和schema保持一致，所有的 `error` 配置属性 `err:` 开头
 * 也可以在 `err:options` 内配置所有的属性，不需要 `err:` 开头
 * 如果配置了`err:xx` 和 `err:options` 内配置了`xx`属性，`err:options`内优先级更高，实际上你可以把所有的参数都配置在 `err:options` 内；这里可以按照个人习惯
 > 注：error-schema 为标准json数据，并非JSON Schema规范语法
 :::

#### error-schema配置在schema中

`0.0.16` 之后版本，`error-schema` 所有配置都支持直接配置在 `schema` 参数中。

> 使用格式类似 [ui-schema配置在schema中](#ui-schema配置在schema中)

error-schema配置演示：重置表单错误信息

::: demo
```html
<template>
    <vue-form
        v-model="formData"
        :schema="schema"
        :ui-schema="uiSchema"
        :error-schema="errorSchema"
    >
    </vue-form>
</template>

<script>
export default {
    name: 'Demo',
    data() {
        return {
            formData: {},
            schema: {
                type: 'object',
                required: [
                    'userName',
                    'homePage',
                    'bio'
                ],
                properties: {
                    userName: {
                        type: 'string',
                        title: '用户名',
                        default: 'Liu.Jun'
                    },
                    homePage: {
                        type: 'string',
                        format: 'uri',
                        title: '个人主页',
                        'err:required': '请输入个人主页地址（schema中配置）',
                        'err:format': '请输入正确的Url地址（schema中配置）'
                    },
                    bio: {
                        type: 'string',
                        title: '签名',
                        minLength: 10
                    },
                    listOfStrings: {
                        type: 'array',
                        title: 'A list of strings',
                        description: '最少包含两个item',
                        uniqueItems: true,
                        minItems: 2,
                        items: {
                            type: 'string',
                            default: 'bazinga'
                        }
                    },
                    fixedItemsList: {
                        type: 'array',
                        title: 'A list of fixed items',
                        items: [
                            {
                                title: 'A string value',
                                type: 'string',
                                maxLength: 2
                            }
                        ]
                    }
                }
            },
            uiSchema: {
                bio: {
                    'ui:type': 'textarea',
                    'ui:placeholder': '请输入 ...',
                    'err:required': '请输入（ui-schema中配置）',
                },
            },
            errorSchema: {
                userName: {
                    'err:options': {
                        required: '请输入用户名'
                    }
                },
                bio: {
                    'err:minLength': '签名最小长度10个字符串'
                },
                listOfStrings: {
                    'err:uniqueItems': '不能包含重复的值',
                    items: {
                        'err:options': {
                            required: '不能为空 ~'
                        }
                    }
                },
                fixedItemsList: {
                    items: [
                        {
                            'err:maxLength': '老铁，最多只能输入两个字符'
                        }
                    ]
                }
            }
        }
    }
}
</script>
```
:::

### custom-formats
* 类型：`object`
* 默认值：`{}`

自定义校验规则，调用 `avj.addFormat` 方法添加新的format，[查看](https://github.com/ajv-validator/ajv#addformatstring-name-stringregexpfunctionobject-format---ajv)

如下，演示添加一种价格校验类型：

::: demo 金额大于0 < 999999.99，保留两位小数
```html
<template>
    <vue-form
        v-model="formData"
        :schema="schema"
        :custom-formats="customFormats"
        :error-schema="errorSchema"
    >
    </vue-form>
</template>

<script>
export default {
    name: 'Demo',
    data() {
        return {
            formData: {},
            schema: {
                type: 'object',
                required: [
                    'price'
                ],
                properties: {
                    price: {
                        type: 'string',
                        title: '价格',
                        description: '请输入数字，最多保留两位小数点，最大值999999.99',
                        format: 'price'
                    }
                }
            },
            errorSchema: {
                price: {
                    'err:options': {
                        required: '请输入价格',
                        format: '最多保留两位小数点，最大值999999.99'
                    }
                }
            },
            customFormats: {
                price(value) {
                    return value !== '' && /^[0-9]\d*$|^\d+(\.\d{1,2})$/.test(value) && value >= 0 && value <= 999999.99
                }
           }
        }
    }
}
</script>
```
:::

### custom-rule
* 类型：`function`
* 默认值：`-`

* 自定义校验规则，实现类似 el-form rules validator 的方式校验表单数据
* [详细查看这里](/zh/guide/validate.html#custom-rule-自定义校验)


### form-footer
* 类型：`object`

```js
// 默认值
formFooter = {
    show: true, // 是否显示默认底部
    okBtn: '保存', // 确认按钮文字
    cancelBtn: '取消' // 取消按钮文字
}
```

### value / v-model
* 类型：`object`
* 默认值：`{}`

表单绑定值，`对于不需要双向绑定的值，可以使用 value props`

### form-props
* 类型：`object`

使用elementUi `el-form` [https://element.eleme.cn/2.13/#/zh-CN/component/form#form-attributes](https://element.eleme.cn/2.13/#/zh-CN/component/form#form-attributes)

```js
// 默认值
formProps = {
    labelPosition: 'top', // 表单域标签的位置
    labelSuffix: '：', //
    inline: false, // 行内表单模式
    labelWidth: 'auto', // 表单域标签的宽度，例如 '50px'
}
```

## 事件 Emit Event
emit所有事件如下：

::: demo
```html
<template>
    <vue-form
        v-model="formData"
        :schema="schema"
        @on-submit="handlerSubmit"
        @on-cancel="handlerCancel"
        @on-change="handlerChange"
    >
    </vue-form>
</template>

<script>
export default {
    name: 'Demo',
    methods: {
        handlerSubmit() {
            const vNode = this.$createElement('pre', JSON.stringify(this.formData, null, 4));
            this.$message({
                type: 'success',
                message: vNode
            });
        },
        handlerCancel() {
            this.$message.warning('点击了取消');
        },
        handlerChange({ oldValue, newValue }) {
            const vNode = this.$createElement('pre', JSON.stringify(newValue, null, 4));
            this.$notify({
                title: '输入数据',
                message: vNode
            });
        },
    },
    data() {
        return {
            formData: {
               name: 'Liu.Jun'
            },
            schema: {
                type: 'object',
                properties: {
                    name: {
                        title: '输入名字',
                        type: 'string'
                    }
                }
            }
        }
    }
};
</script>
```
:::

### on-submit
* 参数(formData)

点击提交按钮，且表单通过校验

> 事件只有在配置了显示默认底部才会触发，[props form-footer](#form-footer)

### on-cancel
* 参数(无)

点击取消按钮
> 事件只有在配置了显示默认底部才会触发，[props form-footer](#form-footer)

### on-change
* 参数(newVal, oldVal)

表单的值发生改变
> 引用类型，只有重新对对象赋值，否则newVal 等于 oldVal 参见 [vue watch](https://cn.vuejs.org/v2/api/#vm-watch)

## 方法 Methods
-- 无

## 插槽 Scope-Slot
* name `default`，自定义form 包含内容，配置后会覆盖默认 `form-footer`

参数为: { formData, formRefFn }

::: tip 参数说明
* `formData` 当前表单元素的值，响应式
* `formRefFn` 为 `function`，调用返回 `el-form` 组件ref实例
:::
如：
::: demo
```html
<template>
    <vue-form
        v-model="formData"
        :schema="schema"
    >
        <div slot-scope="{ formData, formRefFn }">
            <pre style="background-color: #eee;">{{ JSON.stringify(formData, null, 4) }}</pre>
            <p><el-button @click="consoleLog(formRefFn)" type="primary">点击</el-button></p>
        </div>
    </vue-form>
</template>

<script>
export default {
    name: 'Demo',
    methods: {
        consoleLog(getForm) {
            console.log(getForm());
        },
    },
    data() {
        return {
            formData: {
               name: 'Liu.Jun'
            },
            schema: {
                type: 'object',
                properties: {
                    name: {
                        title: '输入名字',
                        type: 'string'
                    }
                }
            }
        }
    }
};
</script>
```
