[TOC]

#### 1. 策略模式

>定义一系列的算法，根据需要选择最佳的策略执行特定的任务

以表单验证场景为例：

```javascript
// 需要校验的数据
const data = {
    model: '',   // 型号
    type: 1,             // 类型
    name: '监测设备',     // 名称
    code: 'a15001',     // 编码
};
// validator对象
const validator = {
    // 配置, 设定策略
    config: {
        model: 'isNotEmpty',
        type: 'isNum',
        name: 'isZhCharacter',
        code: 'isAlphaNum',
    },
    // 算法
    types: {
        isNotEmpty: {
            validate: (value) => value !== "",
            instructions: 'the value cannot be empty',
        },
        isAlphaNum: {
            validate: (value) => value !== "",
            instructions: 'the value can only contain characters and numbers, no special symbols',
        },
        isZhCharacter: {
            validate: (value) => value !== "",
            instructions: 'the value can only be chinese characters',
        },
        isNum: {
            validate: (value) => value !== "",
            instructions: 'the value can only be a valid number, e.g. 1 or 2021',
        },
    },
    // 错误信息
    messages: [],
    hasErrors() {
       return this.messages.length !== 0;
    }
    // 校验操作
    validate(data) {
        this.messages = [];
        // 遍历包括继承所得的可枚举属性
        for (i in data) {
            if (data.hasOwnProperty(i)) {
                type = this.config[i];
				checker = this.types[type];
				
				if (!type) {
					continue; // 不需要验证
				}
                if (!checker) { // 没有对应的验证类型
					throw new Error('No handler to validate type');
				}
                result_ok = checker.validate(data[i]);
				if (!result_ok) {
					msg = `Invalid value for *${i}*, ${checker.instructions}`;
					this.messages.push(msg);
				}
                return this.hasErrors();
            }   
        }
    },
};
validator.validate(data);
if (validator.hasErrors()) {
	console.log(validator.messages.join('n'));
}

output:
// Invalid value for *model*, the value cannot be empty
```



常见使用场景:  **表单验证、菜单栏、tab切换、工具栏**（多个操作切换）

