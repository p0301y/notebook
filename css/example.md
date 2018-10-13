1. div上hover之后的样式常见之一
```
<div class="block"></block>
<style lang='less'>
	.block{
		transition: all 0.2s ease-out;
		margin-bottom:30px;
	}

	.block:hover{
		box-shadow: 0 5px 8px rgba(45,50,55,.2);
    	margin-top: -2px;
    	margin-bottom: 32px;
	}
</style>
```