### Hi there 👋

- typescript
- vue3 & level3 editor & react on the way
- node & deno


### ✨ 在 vue3 中获得 `完整的类型推导` 和 `函数式组件的便捷` 并且不损失 `选项式组件的生命周期`✨

works well with `composition-api` ღ( ´･ᴗ･` ) 💖

```ts
import { SetupContext, defineComponent, h, VNode } from "vue";

export const defineFunctionComponent = <
  P extends {},
  I extends { render: () => VNode }
>(
  component: (props: P, ctx: SetupContext) => I,
  option?: { name: string; inheritAttrs: boolean }
) => {
  const comName = option?.name || component.name || "Anonymous Component";

  const OptionCom = defineComponent({
    name: comName,
    inheritAttrs: option?.inheritAttrs ?? true,
    setup(_, ctx) {
      return component(ctx.attrs as P, ctx);
    },
    render(ctx: { render: () => VNode }) {
      return ctx.render();
    },
  });

  const funtionCom = {
    [comName]: (props: P) => {
      const instance = h(OptionCom, props);
      return instance as unknown as I;
    },
  };

  Reflect.set(OptionCom, "create", funtionCom[comName]);

  type Sign = (props: P) => I & VNode;

  const com = OptionCom as unknown as Sign & {
    create: Sign;
  };

  return com;
};
```
