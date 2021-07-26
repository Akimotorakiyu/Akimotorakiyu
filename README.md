### Hi there 👋

- typescript & HTML & CSS
- vue 2/3
- node & deno

邮箱: 18817832003@qq.com

### ✨ 在 vue3 中获得 `完整的类型推导` 和 `函数式组件的便捷` 并且不损失 `选项式组件的生命周期`✨
**工厂函数组件**
works well with `composition-api` ღ( ´･ᴗ･` ) 💖

```ts
import { SetupContext, defineComponent, h, VNode, onBeforeUpdate, shallowReactive } from 'vue'
/**
 * defineFunctionComponent
 * @author 臭哥哥·湫曗
 * @param component
 * @param option
 * @returns
 */
export const defineFunctionComponent = <
  P extends {},
  I extends { render: () => VNode | JSX.Element }
>(
  component: (props: P, ctx: SetupContext) => I,
  option?: { name?: string; inheritAttrs?: boolean }
) => {
  const comName = option?.name || component.name || 'Anonymous Component'

  const OptionCom = defineComponent({
    name: comName,
    inheritAttrs: option?.inheritAttrs ?? true,
    setup(_, ctx) {
      const props = shallowReactive({})

      const updateProps = () => {
        const keys = Object.keys(ctx.attrs)
        Object.keys(props).forEach((key) => {
          if (!keys.includes(key)) {
            delete props[key]
          }
        })

        Object.entries(ctx.attrs).forEach(([key, value]) => {
          if (props[key] !== value) {
            props[key] = value
          }
        })
      }

      updateProps()

      onBeforeUpdate(() => {
        updateProps()
      })

      return component(props as P, ctx)
    },
    render(ctx: { render: () => VNode }) {
      return ctx.render()
    },
  })

  const funtionCom = {
    [comName]: (props: P) => {
      const instance = h(OptionCom, props)
      return (instance as unknown) as I
    },
  }

  Reflect.set(OptionCom, 'create', funtionCom[comName])

  type Sign = (props: P) => I & JSX.Element

  const com = (OptionCom as unknown) as Sign & {
    create: Sign
  }

  return com
}
```
