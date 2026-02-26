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
      <textarea id="text-input" placeholder="输入要编码的文本（英文字母和数字）" style="width: 100%; height: 100px; padding: 10px; border: 1px solid #ddd; border-radius: 4px; font-family: monospace;"></textarea>
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

// 中文到摩斯电码映射（使用拼音首字母）
const chineseToMorse = {
  '中': '-.-.', '文': '..-.', '测': '-.-.', '试': '...', '你': '-.', '好': '....',
  '我': '--', '是': '...', '摩': '--', '斯': '...', '电': '-.', '码': '-.-',
  '工': '--.', '具': '--.', '编': '-...', '码': '-.-', '解': '.-..', '器': '.-.',
  '一': '.----', '二': '..---', '三': '...--', '四': '....-', '五': '.....',
  '六': '-....', '七': '--...', '八': '---..', '九': '----.', '十': '-----',
  '零': '-----', '壹': '.----', '贰': '..---', '叁': '...--', '肆': '....-',
  '伍': '.....', '陆': '-....', '柒': '--...', '捌': '---..', '玖': '----.', '拾': '-----',
  '啊': '.-', '吧': '-...', '呃': '.', '呆': '-..', '额': '.', '呢': '-.',
  '哦': '---', '呕': '.--', '呀': '-.--', '呜': '.--', '呵': '....',
  '呸': '.--.', '呻': '...', '呼': '....', '呷': '-..-', '呸': '.--.', '呺': '.---'
};

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

  // 支持多种分隔符格式
  let words;
  if (input.includes(' / ')) {
    words = input.split(' / ');
  } else if (input.includes('/')) {
    words = input.split('/');
  } else {
    words = [input];
  }

  const result = words.map(word => {
    let chars;
    if (word.includes(' ')) {
      chars = word.trim().split(' ');
    } else {
      // 如果没有空格，按照点划符号分组
      chars = word.trim().match(/[\.\-]+/g) || [];
    }
    return chars.map(char => morseToChar[char.trim()] || '?').join('');
  }).join(' ');

  document.getElementById('morse-output').textContent = result;
}

function encodeMorse() {
  const input = document.getElementById('text-input').value.trim().toUpperCase();
  if (!input) {
    document.getElementById('text-output').textContent = '请输入文本';
    return;
  }

  const words = input.split(' ');
  const result = words.map(word => {
    const chars = word.split('');
    return chars.map(char => charToMorse[char] || '?').join(' ');
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