<script lang="ts">
	import Card from '@smui/card';
	import { HighlightAuto } from 'svelte-highlight';
	import ashes from 'svelte-highlight/styles/ashes';

	const codeItems = [
		{
			code: `/**
 * 获取assets静态资源，路径为assets
 * @param url 图片名称
 * @returns 
 */
export const getAssetsFile = (url: string): string => {
  return new URL(\`../assets/\$\{url\}\`, import.meta.url).href
}`,
			language: 'typescript'
		},
		{
			code: `/**
 * 深拷贝
 * @param obj 要拷贝的对象
 * @param map 已经拷贝的对象
 * @returns 
 */
export const deepClone = (obj, map = new WeakMap()) => {
  // 如果是基本数据类型，则直接返回
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }

  // 如果已经拷贝过该对象，则直接返回拷贝后的对象
  if (map.has(obj)) {
    return map.get(obj)!;
  }

  // 根据对象的类型进行深拷贝
  const clone = Array.isArray(obj) ? [] : {};

  // 将当前对象和对应的拷贝存储到map中
  map.set(obj, clone);

  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      clone[key] = deepClone(obj[key], map);
    }
  }

  return clone;
}`,
			language: 'typescript'
		},
		{
			code: 'console.log("Hello, world!");',
			language: 'javascript'
		},
		{
			code: 'console.log("Hello, world!");',
			language: 'javascript'
		},
		{
			code: 'console.log("Hello, world!");',
			language: 'javascript'
		},
		{
			code: 'console.log("Hello, world!");',
			language: 'javascript'
		},
		{
			code: 'console.log("Hello, world!");',
			language: 'javascript'
		},
		{
			code: 'console.log("Hello, world!");',
			language: 'javascript'
		},
		{
			code: 'console.log("Hello, world!");',
			language: 'javascript'
		},
		{
			code: 'console.log("Hello, world!");',
			language: 'javascript'
		},
		{
			code: 'console.log("Hello, world!");',
			language: 'javascript'
		}
	];
</script>

<svelte:head>
	{@html ashes}
</svelte:head>

<div class="code-container">
	{#each codeItems as item, i}
		<Card variant="outlined">
			<code class="grid-item">
				<HighlightAuto code={item.code} />
			</code>
		</Card>
	{/each}
</div>

<style lang="scss" scoped>
	.code-container {
		height: 100vh;
		background: #343434;
		column-count: 2;
		column-gap: 10px;
		padding: 10px;
		overflow: auto;
	}

	.grid-item {
		height: 100%;
		box-sizing: border-box;
		break-inside: avoid;
		padding: 8px;
		background-color: var(--mdc-theme-secondary, #333);
		color: var(--mdc-theme-on-secondary, #fff);
	}
</style>
