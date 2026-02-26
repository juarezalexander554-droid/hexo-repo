---
title: About
date: 2026-02-27
---

# 关于网页

## 摩斯电码解码工具

在下方输入摩斯电码，点击解码查看对应的文字。

<div id="morse-decoder" style="padding: 20px; background: #f5f5f5; border-radius: 8px; margin: 20px 0;">
  <div style="margin-bottom: 15px;">
    <label for="morse-input" style="display: block; margin-bottom: 8px; font-weight: bold;">摩斯电码输入：</label>
    <textarea id="morse-input" placeholder="输入摩斯电码，用空格分隔字符，用 / 分隔单词&#10;例如：.... . .-.. .-.. --- / .-- --- .-. .-.. -.." style="width: 100%; height: 100px; padding: 10px; border: 1px solid #ddd; border-radius: 4px; font-family: monospace;"></textarea>
  </div>
  
  <div style="margin-bottom: 15px;">
    <button onclick="decodeMorse()" style="padding: 10px 20px; background: #007bff; color: white; border: none; border-radius: 4px; cursor: pointer; font-size: 16px;">解码</button>
    <button onclick="clearMorse()" style="padding: 10px 20px; background: #6c757d; color: white; border: none; border-radius: 4px; cursor: pointer; font-size: 16px; margin-left: 10px;">清空</button>
  </div>
  
  <div>
    <label for="morse-output" style="display: block; margin-bottom: 8px; font-weight: bold;">解码结果：</label>
    <div id="morse-output" style="padding: 10px; background: white; border: 1px solid #ddd; border-radius: 4px; min-height: 50px; font-size: 18px;"></div>
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
  '----.': '9', '.-.-.-': '.', '--..--': ',', '..--..': '?', '.----.': "'",
  '-.-.-.': '!', '-..-.': '/', '-.--.': '(', '-.--.-': ')', '.-...': '&',
  '---...': ':', '-.-.-': ';', '-...-': '=', '.-.-.': '+', '-....-': '-',
  '..--.-': '_', '.-..-.': '"', '...-..-': '$', '.--.-.': '@'
};

function decodeMorse() {
  const input = document.getElementById('morse-input').value.trim();
  if (!input) {
    document.getElementById('morse-output').textContent = '请输入摩斯电码';
    return;
  }
  
  const words = input.split(' / ');
  const result = words.map(word => {
    const chars = word.split(' ');
    return chars.map(char => morseToChar[char] || '?').join('');
  }).join(' ');
  
  document.getElementById('morse-output').textContent = result;
}

function clearMorse() {
  document.getElementById('morse-input').value = '';
  document.getElementById('morse-output').textContent = '';
}

// 回车键解码
document.getElementById('morse-input').addEventListener('keypress', function(e) {
  if (e.key === 'Enter' && e.ctrlKey) {
    decodeMorse();
  }
});
</script>

---

## 使用说明

- **摩斯电码格式**: 用空格分隔字符，用  分隔单词
- **示例**:  解码为 
- **支持**: 大小写字母、数字、常见标点符号

---

*一个人的宇宙 - 在画面与声音的联系中无限探索*