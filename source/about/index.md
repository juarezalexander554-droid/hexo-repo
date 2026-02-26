---
title: About
date: 2026-02-27
---

<!-- 摩斯电码编码解码工具 -->
<div id="morse-tool" style="padding: 20px; background: #f5f5f5; border-radius: 8px; margin: 20px 0;">
  <h2>摩斯电码编码解码工具</h2>

  <!-- 标签页切换 -->
  <div style="margin-bottom: 15px; border-bottom: 2px solid #ddd;">
    <button onclick="switchTab('decode')" id="decode-tab" style="padding: 10px 20px; background: #007bff; color: white; border: none; cursor: pointer; font-size: 16px; border-radius: 4px 4px 0 0;">解码</button>
    <button onclick="switchTab('encode')" id="encode-tab" style="padding: 10px 20px; background: #6c757d; color: white; border: none; cursor: pointer; font-size: 16px; margin-left: 5px; border-radius: 4px 4px 0 0;">编码</button>
  </div>

  <!-- 解码面板 -->
  <div id="decode-panel" style="display: block;">
    <div style="margin-bottom: 15px;">
      <label for="morse-input" style="display: block; margin-bottom: 8px; font-weight: bold;">摩斯电码输入：</label>
      <textarea id="morse-input" placeholder="输入摩斯电码，用空格分隔字符，用 / 分隔单词" style="width: 100%; height: 100px; padding: 10px; border: 1px solid #ddd; border-radius: 4px; font-family: monospace;"></textarea>
    </div>
    <div style="margin-bottom: 15px;">
      <button onclick="decodeMorse()" style="padding: 10px 20px; background: #007bff; color: white; border: none; border-radius: 4px; cursor: pointer; font-size: 16px;">解码</button>
      <button onclick="clearDecode()" style="padding: 10px 20px; background: #6c757d; color: white; border: none; border-radius: 4px; cursor: pointer; font-size: 16px; margin-left: 10px;">清空</button>
    </div>
    <div>
      <label for="morse-output" style="display: block; margin-bottom: 8px; font-weight: bold;">解码结果：</label>
      <div id="morse-output" style="padding: 10px; background: white; border: 1px solid #ddd; border-radius: 4px; min-height: 50px; font-size: 18px;"></div>
    </div>
  </div>

  <!-- 编码面板 -->
  <div id="encode-panel" style="display: none;">
    <div style="margin-bottom: 15px;">
      <label for="text-input" style="display: block; margin-bottom: 8px; font-weight: bold;">文本输入：</label>
      <textarea id="text-input" placeholder="输入要编码的文本（英文字母、数字、中文）" style="width: 100%; height: 100px; padding: 10px; border: 1px solid #ddd; border-radius: 4px; font-family: monospace;"></textarea>
    </div>
    <div style="margin-bottom: 15px;">
      <button onclick="encodeMorse()" style="padding: 10px 20px; background: #28a745; color: white; border: none; border-radius: 4px; cursor: pointer; font-size: 16px;">编码</button>
      <button onclick="clearEncode()" style="padding: 10px 20px; background: #6c757d; color: white; border: none; border-radius: 4px; cursor: pointer; font-size: 16px; margin-left: 10px;">清空</button>
    </div>
    <div>
      <label for="text-output" style="display: block; margin-bottom: 8px; font-weight: bold;">摩斯电码结果：</label>
      <div id="text-output" style="padding: 10px; background: white; border: 1px solid #ddd; border-radius: 4px; min-height: 50px; font-size: 18px; font-family: monospace;"></div>
    </div>
  </div>
</div>

<script>
const morseToChar = {
  '.-': 'A', '-...': 'B', '-.-.': 'C', '-..': 'D', '.': 'E',
  '..-.': 'F', '--.': 'G', '....': 'H', '..': 'I', '.---': 'J',
  '-.-': 'K', '.-..': 'L', '--': 'M', '-.': 'N', '---': 'O',
  '.--.': 'P', '--.-': 'Q', '.-.': 'R', '...': 'S', '-': 'T',
  '..-': 'U', '...-': 'V', '.--': 'W', '-..-': 'X', '-.--': 'Y',
  '--..': 'Z', '-----': '0', '.----': '1', '..---': '2', '...--': '3',
  '....-': '4', '.....': '5', '-....': '6', '--...': '7', '---..': '8',
  '----.': '9'
};

// 中文到摩斯电码映射（使用Unicode编码）
const chineseToMorse = {};
const morseToChineseChar = {};

// 生成映射表：每个汉字用其Unicode编码转摩斯码
function generateChineseMorseMap() {
  const chineseChars = '你好中文摩斯电码工具编解器一二三四五六七八九十零壹贰叁肆伍陆柒捌玖拾啊吧呃呆额呢哦呕呀呜呵呸呻呼呷呺我是测试爱学习题目字本书人大小山水火木金土日月星天地风雨雪云雷光声色香味触心手足头眼耳鼻口舌齿唇喉肺肝肾脾胃肠骨肉血气精神魂魄生死病痛苦乐喜怒哀惧恨欲望思想念意志力能做行走跑跳飞游泳爬坐站躺睡醒吃喝饭菜鱼虾蟹贝蛋奶油盐糖酱醋酒茶咖啡果汁冰冻甜酸辣咸淡臭美丑好坏善恶真假正邪清浊明暗亮白黑红绿蓝黄紫橙粉灰棕银铜铁锡铅锌汞硫碳氮氧氢氯钠钾钙';

  for (let i = 0; i < chineseChars.length; i++) {
    const char = chineseChars[i];
    const code = char.charCodeAt(0);
    // 将Unicode编码转换为摩斯码（用点划表示二进制）
    const binary = code.toString(2).padStart(16, '0');
    const morse = binary.split('').map(bit => bit === '0' ? '.' : '-').join(' ');

    chineseToMorse[char] = morse;
    morseToChineseChar[morse] = char;
  }
}

generateChineseMorseMap();

const charToMorse = {};
Object.keys(morseToChar).forEach(morse => {
  charToMorse[morseToChar[morse]] = morse;
});
Object.keys(chineseToMorse).forEach(char => {
  charToMorse[char] = chineseToMorse[char];
});

function switchTab(tab) {
  const decodePanel = document.getElementById('decode-panel');
  const encodePanel = document.getElementById('encode-panel');
  const decodeTab = document.getElementById('decode-tab');
  const encodeTab = document.getElementById('encode-tab');

  if (tab === 'decode') {
    decodePanel.style.display = 'block';
    encodePanel.style.display = 'none';
    decodeTab.style.background = '#007bff';
    encodeTab.style.background = '#6c757d';
  } else {
    decodePanel.style.display = 'none';
    encodePanel.style.display = 'block';
    decodeTab.style.background = '#6c757d';
    encodeTab.style.background = '#28a745';
  }
}

function decodeMorse() {
  const input = document.getElementById('morse-input').value.trim();
  if (!input) {
    document.getElementById('morse-output').textContent = '请输入摩斯电码';
    return;
  }

  let words;
  if (input.includes(' / ')) {
    words = input.split(' / ');
  } else if (input.includes('/')) {
    words = input.split('/');
  } else {
    words = [input];
  }

  const result = words.map(word => {
    // 先尝试作为整体查找中文映射
    const trimmed = word.trim();
    if (morseToChineseChar[trimmed]) {
      return morseToChineseChar[trimmed];
    }

    // 否则按字符分割
    let chars;
    if (word.includes(' ')) {
      chars = word.trim().split(' ');
    } else {
      chars = word.trim().match(/[\.\-]+/g) || [];
    }

    return chars.map(char => {
      const trimmedChar = char.trim();
      if (morseToChineseChar[trimmedChar]) {
        return morseToChineseChar[trimmedChar];
      }
      return morseToChar[trimmedChar] || '?';
    }).join('');
  }).join(' ');

  document.getElementById('morse-output').textContent = result;
}

function encodeMorse() {
  const input = document.getElementById('text-input').value.trim();
  if (!input) {
    document.getElementById('text-output').textContent = '请输入文本';
    return;
  }

  const words = input.split(' ');
  const result = words.map(word => {
    const chars = word.split('');
    return chars.map(char => {
      const upper = char.toUpperCase();
      return charToMorse[upper] || charToMorse[char] || '?';
    }).join(' ');
  }).join(' / ');

  document.getElementById('text-output').textContent = result;
}

function clearDecode() {
  document.getElementById('morse-input').value = '';
  document.getElementById('morse-output').textContent = '';
}

function clearEncode() {
  document.getElementById('text-input').value = '';
  document.getElementById('text-output').textContent = '';
}
</script>
