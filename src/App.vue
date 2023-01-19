<template>
	<div class="calculator">
		<div class="result" style="grid-area: result">
			{{ data.result }}
		</div>

		<button :class="{ active: data.isActive === 'clear' }" style="grid-area: ac" @click="_clear">AC</button>
		<button :class="{ active: data.isActive === 'calculate', equal: true }" style="grid-area: equal"
			@click="_calculate">=</button>

		<button :class="{ active: data.isActive === '儿子' }" style="grid-area: son" @click="_append('儿子')">子</button>
		<button :class="{ active: data.isActive === '女儿' }" style="grid-area: daughter"
			@click="_append('女儿')">女</button>

		<button :class="{ active: data.isActive === '哥哥' }" style="grid-area: brother" @click="_append('哥哥')">兄</button>
		<button :class="{ active: data.isActive === '弟弟' }" style="grid-area: younger_brother"
			@click="_append('弟弟')">弟</button>

		<button :class="{ active: data.isActive === '老公', disabled: data.sex === 1 }" style="grid-area: husband"
			@click="_append('老公')">夫</button>
		<button :class="{ active: data.isActive === '老婆', disabled: data.sex === 0 }" style="grid-area: wife"
			@click="_append('老婆')">妻</button>

		<button :class="{ active: data.isActive === '姐姐' }" style="grid-area: sister" @click="_append('姐姐')">姐</button>
		<button :class="{ active: data.isActive === '妹妹' }" style="grid-area: younger_sister"
			@click="_append('妹妹');">妹</button>

		<button :class="{ active: data.isActive === '爸爸' }" style="grid-area: dad" @click="_append('爸爸')">父</button>
		<button :class="{ active: data.isActive === '妈妈' }" style="grid-area: mom" @click="_append('妈妈')">母</button>

		<button :class="{ active: data.isActive === 'delete' }" style="grid-area: back" @click="_delete">
			<svg style="width:24px;height:24px;margin-top: 6px;" viewBox="0 0 24 24">
				<path fill="currentColor"
					d="M22,3H7C6.31,3 5.77,3.35 5.41,3.88L0,12L5.41,20.11C5.77,20.64 6.31,21 7,21H22A2,2 0 0,0 24,19V5A2,2 0 0,0 22,3M19,15.59L17.59,17L14,13.41L10.41,17L9,15.59L12.59,12L9,8.41L10.41,7L14,10.59L17.59,7L19,8.41L15.41,12" />
			</svg>
		</button>
		<button class="selector" style="grid-area: selector" @click="_selector">
			<div>{{ (data.sex == 0) ? '女' : '男' }}</div>
		</button>
	</div>
</template>


<script setup lang="ts">
// @ts-ignore
import relationship from "relationship.js"

import { reactive, watch } from 'vue'

type data = {
  timer: Object | any,
}

let data = reactive({
	search: '',
	append: '',
	result: '------',
	sex: 1,
	isActive: '',
	timer: 0,
});

/**
 * 加载中
 */

let loaded = reactive({
	value: false
});

const fn = () => {
	if (document.readyState == "complete") {
		window.removeEventListener("load", fn);
		complele();
	}
};

window.addEventListener("load", fn, false);

const complele = () => {
	setTimeout(() => {
		loaded.value = true;
	}, 1895);
};

watch(data, (n, o) => {
	if ('爸爸,老公,儿子,哥哥,弟弟'.indexOf(n.append) > -1) {
		data.sex = 1;
	} else if ('妈妈,老婆,女儿,姐姐,妹妹'.indexOf(n.append) > -1) {
		data.sex = 0;
	}
});

/**
 * 切换查询对象性别
 * 
 */
const _selector = () => {
	data.sex = (data.sex == 1) ? 0 : 1;
}

const _clear = () => {

	_active.call(_append, "clear");

	data.search = '';
	data.append = '';
	data.result = '------';
}

const _delete = () => {

	_active.call(_append, "delete");

	let index = data.search.lastIndexOf('的'),
		search = data.search;
	index = Math.max(0, index);
	if (search) {
		search = search.slice(0, index);
		data.search = search;
		let _i = search.lastIndexOf('的');
		_i = Math.max(0, _i);
		data.append = search.slice(_i + 1);
		data.result = search;
	} else {
		data.search = '';
		data.append = '';
		data.result = '------';
	}
}

const _append = (v: string) => {

	_active.call(_append, v);

	let arr = data.search.split('的'),
		search = data.search

	if (arr.length > 20) {
		data.search = '';
		data.append = '';
		data.result = '------';
	} else {
		let a = (search ? search + '的' + v : v);
		data.append = v;
		data.search = data.result = a;
	}
}

const _calculate = () => {

	_active.call(_append, 'calculate');

	if (!data.search) {
		data.append = '';
		data.result = '------';
	} else {
		let _r = relationship({ text: data.search, sex: data.sex });
		if (_r.length > 0) {
			let result = '';
			for (let i = 0; i < _r.length; i++) {
				if (i != 0 && i != _r.length) result += ' / ';
				result += _r[i];
			}
			data.result = result;
		} else {
			data.result = '未知';
		}
	}
}

const _active = (n: string) => {
	if (data.timer) clearTimeout(data.timer);
	data.isActive = n;
	data.timer = setTimeout(() => {
		data.isActive = '';
	}, 120);
}
</script>