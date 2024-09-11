<script lang="ts">
	import Tab, { Label } from '@smui/tab';
	import TabBar from '@smui/tab-bar';
	import Card from '@smui/card';
	import { HighlightAuto } from 'svelte-highlight';
	import ashes from 'svelte-highlight/styles/ashes';

	const tagItems: string[] = [
		'typescript',
		'clone',
		'tree',
		'format',
		'file',
		'dom',
		'array',
		'window'
	];

	type CodeItem = {
		code: string;
		language: string;
		tag: string[];
	};

	let codeItems: CodeItem[] = [
		{
			code: `/**
 * 获取assets静态资源，路径为assets
 * @param url 图片名称
 * @returns 
 */
export const getAssetsFile = (url: string): string => {
  return new URL(\`../assets/\${url}\`, import.meta.url).href
}`,
			language: 'typescript',
			tag: ['typescript', 'assets']
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
			language: 'typescript',
			tag: ['typescript', 'clone']
		},
		{
			code: `/**
 * @description 树状结构扁平化
 * @param {boolean} type 是否需要返回父元素
 * @returns {array} 返回扁平化数组
 */
interface TreeNode {
  children?: TreeNode[];
  [key: string]: any;
}

export function flatten(arr: TreeNode[], type?: boolean): TreeNode[] {
  return arr.reduce((flat: TreeNode[], toFlatten: TreeNode) => {
    return flat.concat(
      Array.isArray(toFlatten.children)
        ? type
          ? [toFlatten].concat(flatten(toFlatten.children, type))
          : flatten(toFlatten.children, type)
        : toFlatten
    );
  }, []);
}`,
			language: 'typescript',
			tag: ['typescript', 'tree']
		},
		{
			code: `/**
 * @description 格式化金额
 * @param {string | number} num 金额
 * @param {number} dec 小数位数
 * @returns {string} 格式化后的金额
 */
export function formatAmount(num?: string | number, dec: number = 2): string {
  if (!num) return "0.00";
  return (parseFloat(num as string).toFixed(dec) + "").replace(
    /\B(?=(\d{3})+(?!\d))/g,
    ","
  );
}`,
			language: 'typescript',
			tag: ['typescript', 'format']
		},
		{
			code: `/**
 * @description 将文件转换为json
 * @param {Blob} file 文件
 * @returns {Promise<any>} 返回json
 */
export function fileToJson(file: Blob): Promise<any> {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = (res) => {
      const { result } = res.target as FileReader; // 得到字符串
      const data = JSON.parse(result as string); // 解析成json对象
      console.log(data);
      resolve(data);
    }; // 成功回调
    reader.onerror = (err) => {
      reject(err);
    }; // 失败回调
    reader.readAsText(new Blob([file]), "utf-8"); // 按照utf-8编码解析
  });
}`,
			language: 'typescript',
			tag: ['typescript', 'file']
		},
		{
			code: `/**
 * @description: 获取指定class的父节点
 * @param {HTMLElement} element 元素
 * @param {string} className 类名
 * @returns {HTMLElement | null} 父节点
 */
function getParents(element: HTMLElement, className: string): HTMLElement | null {
  let node: HTMLElement | null = element.parentElement;
  function getParentNode(element, className) {
    if (element.className) {
      if (
        element &&
        element.classList.contains(className) &&
        element.tagName.toLowerCase() != "body"
      ) {
        node = element as HTMLElement;
      } else {
        getParentNode(element.parentElement, className);
      }
    }
  }
  getParentNode(element, className);
  return node;
}`,
			language: 'typescript',
			tag: ['typescript', 'dom']
		},
		{
			code: `/**
 * @description: 根据数组中某个对象值去重
 * @param {T[]} arr 数组
 * @param {string} key 对象值
 * @returns {T[]} 去重后的数组
 */
export function unique<T = any>(arr: T[], key: string): T[] {
  const map = new Map();
  return arr.filter((item) => {
    const _item = item as any;
    return !map.has(_item[key]) && map.set(_item[key], 1);
  });
}`,
			language: 'typescript',
			tag: ['typescript', 'array']
		},
		{
			code: `/**
 * @description: es6数组去重复
 * @param {T[]} arr 数组
 * @returns {T[]} 去重后的数组
 */
export function es6Unique<T>(arr: T[]): T[] {
  return Array.from(new Set(arr));
}`,
			language: 'typescript',
			tag: ['typescript', 'array']
		},
		{
			code: `/**
 * @description: 打开新窗口
 * @param {string} url 链接
 * @param {object} opt 配置
 * @returns {void} 无返回值
 */
export function openWindow(
  url: string,
  opt?: {
    target?: TargetContext | string;
    noopener?: boolean;
    noreferrer?: boolean;
  }
) {
  const { target = "__blank", noopener = true, noreferrer = true } = opt || {};
  const feature: string[] = [];
  noopener && feature.push("noopener=yes");
  noreferrer && feature.push("noreferrer=yes");
  window.open(url, target, feature.join(","));
}`,
			language: 'typescript',
			tag: ['typescript', 'window']
		}
	];

	let filterCodeItems: CodeItem[] = [...codeItems];

	let activeTab = tagItems[0];

	const handleTabChange = (event: CustomEvent<string>) => {
		filterCodeItems = codeItems.filter((item) => item.tag.includes(activeTab));
	};
</script>

<svelte:head>
	{@html ashes}
</svelte:head>

<div class="tag-container">
	<TabBar tabs={tagItems} let:tab bind:active={activeTab} on:click={handleTabChange} role="codeItems">
		<Tab {tab}>
			<Label>{tab}</Label>
		</Tab>
	</TabBar>
</div>

<div class="code-container">
	{#each filterCodeItems as item, i}
		<Card>
			<code class="grid-item">
				<HighlightAuto code={item.code} />
			</code>
		</Card>
	{/each}
</div>

<style lang="scss" scoped>
	.code-container {
		background: #343434;
		column-count: 2;
		column-gap: 12px;
		padding: 12px;
		padding-top: 0;
		overflow: auto;
	}

	.grid-item {
		height: 100%;
		padding-top: 12px;
		box-sizing: border-box;
		break-inside: avoid;
		background-color: var(--mdc-theme-secondary, #333);
		color: var(--mdc-theme-on-secondary, #fff);
	}
</style>
