# svelte-float-window
A Svelte Floating Window component.

## Include it in your Svelte App

```
import FloatWindow from "svelte-float-window";
```

## Use it in your Svelte App

```

let all_window_scales = [
	{ "w" : w_percent_0, "h" : h_percent_0 },
	{ "w" : w_percent_1, "h" : h_percent_1 }
]

<FloatWindow title={FirstWindowTitle} index={0} scale_size_array={all_window_scales[0]} >
	<InternalComponentType1 {...one_thing} />
</FloatWindow>


<FloatWindow title={SecondWindowTitle} index={1} scale_size_array={all_window_scales[1]} >
	<InternalComponentType1 {...another_thing}  />
</FloatWindow>

```

## Help

[Issues](https://github.com/copious-world/svelte-float-window/issues)

In general, there are interesting interface problems that are being solved as part of [copious-world](https://github.com/copious-world). And, in creating this repository, the author is looking to pass on some experience and get infrastructure taken care of for site building, specific topics of use, and to help the world in general get their enterprises up quickly and cheaply. This author favor the independent operator, the libertarian mindset, and values family and community.



