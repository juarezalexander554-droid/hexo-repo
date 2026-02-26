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

// 中文到摩斯电码映射（混合方案：常用字用直接映射，其他用拼音首字母）
const chineseToMorse = {
  '你': '-.  ..', '好': '....  .-  ---', '中': '-.-.  .  -.  -.', '文': '..-.  .  -.  -.',
  '摩': '--  ---', '斯': '...  ...', '电': '-.  ..  .-..  -.', '码': '--  .-  ---',
  '工': '--.  ---  -.  -.', '具': '--.  ..-  ...', '编': '-...  ..  .-..  -.', '解': '.  ..  .  ...',
  '器': '--.-  ..  ...', '一': '..  ...', '二': '.-..  ..  ...', '三': '...  .-..  -.',
  '四': '...  ...  ...', '五': '..-.  ..  ...-', '六': '.-..  ..  ..-', '七': '--.-  ..  ...',
  '八': '-...  .-', '九': '.  ..  ..-', '十': '...  .  ...', '零': '.-..  ..  -.',
  '壹': '..  ...', '贰': '.-..  ..  ...', '叁': '...  .-..  -.', '肆': '...  ...  ...',
  '伍': '..-.  ..  ...-', '陆': '.-..  ..  ..-', '柒': '--.-  ..  ...', '捌': '-...  .-',
  '玖': '.  ..  ..-', '拾': '...  .  ...', '啊': '.-', '吧': '-...  .-', '呃': '.',
  '呆': '-..  .-', '额': '.', '呢': '-.  .', '哦': '---', '呕': '---  ..-', '呀': '-.--  .-',
  '呜': '.--  ..-', '呵': '....  .-', '呸': '.-.-.  ..', '呻': '...  .  -.', '呼': '....  ..-',
  '呷': '-..-  ..  .-', '呺': '.---  ..-', '我': '.--  ---', '是': '...  ...', '测': '-.-.  .  ...',
  '试': '...  ...', '爱': '.-', '学': '.-..  ..-  .  ...', '习': '-..-  ..  ...', '题': '-',
  '目': '--  ..-', '字': '--  ..  ...', '本': '-...  .  -.', '书': '...  .  ..-', '人': '.-..  .  -.',
  '大': '-..  .-', '小': '-..-  ..  .-  ---', '山': '...  .  .-..  -.', '水': '...  ..-  ..  ...',
  '火': '..-.  ..-  ---', '木': '--  ..-', '金': '.  ..  -.', '土': '-  ..-', '日': '.-..  ..  ...',
  '月': '-.--  ..-  .  ...', '星': '-..-  ..  -.  -.', '天': '-  ---  ..-', '地': '-..  ..  ...',
  '风': '..-.  .  -.  -.', '雨': '-.--  ..-  ...', '雪': '-..-  ..-  .  ...', '云': '-.--  ..-  -.',
  '雷': '.-..  .  ..  ...', '光': '--.  ..-  .-..  -.', '声': '...  .  .  -.', '色': '...  .  ...',
  '香': '-..-  ..  .-..  -.', '味': '.--  .  ..  ...', '触': '-.-.  ..-  ---', '心': '-..-  ..  -.',
  '手': '...  ..-  ---', '足': '--..  ..-  ...', '头': '-  ---  ..-', '眼': '-.--  .-..  -.',
  '耳': '.-..  ...', '鼻': '-...  ..  ...', '口': '-.-  ---  ..-', '舌': '...  .  ...',
  '齿': '-.-.  .  ...', '唇': '-.-.  ..-  -.', '喉': '....  ---  ..-', '肺': '.--.  ..-  ...',
  '肝': '--.  .-..  -.', '肾': '...  .  -.', '脾': '.-.-.  ..  ...', '胃': '.--  .  ..  ...',
  '肠': '-.-.  .  .-..  -.', '骨': '--.  ..-  ...', '肉': '.-..  ---  ..-', '血': '-..-  ..-  .  ...',
  '气': '--.-  ..  ...', '精': '.  ..  -.  -.', '神': '...  .  -.', '魂': '....  ..-  -.',
  '魄': '.-.-.  .-', '生': '...  .  -.  -.', '死': '-..  ...', '病': '-...  ..  -.  -.',
  '痛': '-  ---  -.  -.', '苦': '-.-  ..-  ...', '乐': '.-..  .  ...', '喜': '-..-  ..  ...',
  '怒': '-.  ..-  ---', '哀': '.-', '惧': '.  ..  ..-  ...', '恨': '....  .  -.',
  '欲': '-.--  ..-  ...', '望': '.--  .-..  -.  -.', '思': '...  ...', '想': '-..-  ..  .-..  -.',
  '念': '-.  ..  .-..  -.', '意': '..  ...', '志': '--  ...', '力': '.-..  ..  ...',
  '能': '-.  .  -.  -.', '做': '--  ..-  ---', '行': '-..-  ..  -.  -.', '走': '--  ..-  ---',
  '跑': '.-.-.  .-  ---', '跳': '-  ..  .-  ---', '飞': '..-.  .  ..  ...', '游': '-.--  ---  ..-',
  '泳': '-.--  ---  -.  -.', '爬': '.-.-.  .-', '坐': '--  ..-  ---', '站': '--  .  .-..  -.',
  '躺': '-  ..  .-..  -.', '睡': '...  ..-  ..  ...', '醒': '-..-  ..  -.  -.', '吃': '-.-.  ...',
  '喝': '....  .  ...', '饭': '..-.  .-..  -.', '菜': '-.-.  .-', '鱼': '-.--  ..-  ...',
  '虾': '-..-  ..  .-', '蟹': '-..-  ..  .  ...', '贝': '-...  .  ..  ...', '蛋': '-..  .-..  -.',
  '奶': '-.  .-', '油': '---  ..  ..-', '盐': '-.--  .-..  -.', '糖': '-  .-..  -.  -.',
  '酱': '.  ..  .-..  -.', '醋': '-.-.  ..-  ...', '酒': '.  ..  ..-', '茶': '-.-.  .-',
  '咖': '-.-  .-', '啡': '..-.  .  ..  ...', '果': '--.  ..-  ---', '汁': '--  ...',
  '冰': '-...  ..  -.  -.', '冻': '-..  ---  -.  -.', '甜': '-  ..  .-..  -.', '酸': '...  ..-  .-..  -.',
  '辣': '.-..  .-', '咸': '-..-  ..  .-..  -.', '淡': '-..  .-..  -.', '臭': '-.-.  ---  ..-',
  '美': '--  .  ..  ...', '丑': '-.-.  ..-  ---', '坏': '....  ..-  .-', '善': '...  .  .-..  -.',
  '恶': '.  ...', '真': '--  --..  .  -.', '假': '.  ..  .-', '正': '--  .  -.  -.',
  '邪': '-..-  ..  .-', '清': '--.-  ..  -.  -.', '浊': '--  ..-  ---', '明': '--  ..  -.  -.',
  '暗': '.-..  -.', '亮': '.-..  ..  .-..  -.', '白': '-...  .-', '黑': '....  .  ..  ...',
  '红': '....  ---  -.  -.', '绿': '.-..  ..-  ...', '蓝': '-...  .-..  -.', '黄': '....  ..-  .-..  -.',
  '紫': '--  ...', '橙': '---  .  -.  -.', '粉': '..-.  .  -.', '灰': '....  ..-  ..  ...',
  '棕': '--  ---  -.  -.', '银': '-.--  ..  -.', '铜': '-  ---  -.  -.', '铁': '-  ..  .  ...',
  '锡': '-..-  ..  ...', '铅': '--.-  ..  .-..  -.', '锌': '--  ..  -.', '汞': '....  ---  -.  -.',
  '硫': '.-..  ..-  ...', '碳': '-  .-..  -.', '氮': '-.  .  -..', '氧': '---  .-..  -.  -.',
  '氢': '--.-  ..  -.  -.', '氯': '-.-.  .-..  ..-', '钠': '-.  .-', '钾': '-.-  .-', '钙': '-.-.  .-'
};

// 摩斯电码到中文的反向映射
const morseToChineseChar = {
  '-.  ..': '你', '....  .-  ---': '好', '-.-.  .  -.  -.': '中', '..-.  .  -.  -.': '文',
  '--  ---': '摩', '...  ...': '斯', '-.  ..  .-..  -.': '电', '--  .-  ---': '码',
  '--.  ---  -.  -.': '工', '--.  ..-  ...': '具', '-...  ..  .-..  -.': '编', '.  ..  .  ...': '解',
  '--.-  ..  ...': '器', '..  ...': '一', '.-..  ..  ...': '二', '...  .-..  -.': '三',
  '...  ...  ...': '四', '..-.  ..  ...-': '五', '.-..  ..  ..-': '六', '--.-  ..  ...': '七',
  '-...  .-': '八', '.  ..  ..-': '九', '...  .  ...': '十', '.-..  ..  -.': '零',
  '.-': '啊', '-...  .-': '吧', '.': '呃', '-..  .-': '呆', '-.  .': '呢', '---': '哦',
  '---  ..-': '呕', '-.--  .-': '呀', '.--  ..-': '呜', '....  .-': '呵', '.-.-.  ..': '呸',
  '...  .  -.': '呻', '....  ..-': '呼', '-..-  ..  .-': '呷', '.---  ..-': '呺',
  '.--  ---': '我', '-.  .-': '是', '-.-.  .  ...': '测', '....  ..-  .-..  -.': '试',
  '.-..  ..-  .  ...': '学', '-..-  ..  ...': '习', '-': '题', '--  ..-': '目',
  '--  ..  ...': '字', '-...  .  -.': '本', '...  .  ..-': '书', '.-..  .  -.': '人',
  '-..  .-': '大', '-..-  ..  .-  ---': '小', '...  .  .-..  -.': '山', '...  ..-  ..  ...': '水',
  '..-.  ..-  ---': '火', '.  ..  -.': '金', '-  ..-': '土', '.-..  ..  ...': '日',
  '-.--  ..-  .  ...': '月', '-..-  ..  -.  -.': '星', '-  ---  ..-': '天', '-..  ..  ...': '地',
  '..-.  .  -.  -.': '风', '-.--  ..-  ...': '雨', '-..-  ..-  .  ...': '雪', '-.--  ..-  -.': '云',
  '.-..  .  ..  ...': '雷', '--.  ..-  .-..  -.': '光', '...  .  .  -.': '声', '...  .  ...': '色',
  '-..-  ..  .-..  -.': '香', '.--  .  ..  ...': '味', '-.-.  ..-  ---': '触', '-..-  ..  -.': '心',
  '...  ..-  ---': '手', '--..  ..-  ...': '足', '-  ---  ..-': '头', '-.--  .-..  -.': '眼',
  '.-..  ...': '耳', '-...  ..  ...': '鼻', '-.-  ---  ..-': '口', '...  .  ...': '舌',
  '-.-.  .  ...': '齿', '-.-.  ..-  -.': '唇', '....  ---  ..-': '喉', '.--.  ..-  ...': '肺',
  '--.  .-..  -.': '肝', '...  .  -.': '肾', '.-.-.  ..  ...': '脾', '.--  .  ..  ...': '胃',
  '-.-.  .  .-..  -.': '肠', '--.  ..-  ...': '骨', '.-..  ---  ..-': '肉', '-..-  ..-  .  ...': '血',
  '--.-  ..  ...': '气', '.  ..  -.  -.': '精', '...  .  -.': '神', '....  ..-  -.': '魂',
  '.-.-.  .-': '魄', '...  .  -.  -.': '生', '-..  ...': '死', '-...  ..  -.  -.': '病',
  '-  ---  -.  -.': '痛', '-.-  ..-  ...': '苦', '.-..  .  ...': '乐', '-..-  ..  ...': '喜',
  '-.  ..-  ---': '怒', '....  .  -.': '恨', '-.--  ..-  ...': '欲', '.--  .-..  -.  -.': '望',
  '-..-  ..  .-..  -.': '想', '-.  ..  .-..  -.': '念', '..  ...': '意', '--  ...': '志',
  '.-..  ..  ...': '力', '-.  .  -.  -.': '能', '--  ..-  ---': '做', '-..-  ..  -.  -.': '行',
  '.-.-.  .-  ---': '跑', '-  ..  .-  ---': '跳', '..-.  .  ..  ...': '飞', '-.--  ---  ..-': '游',
  '-.--  ---  -.  -.': '泳', '-.-.  .-': '爬', '...  .  .-..  -.': '站', '-  ..  .-..  -.': '躺',
  '...  ..-  ..  ...': '睡', '-..-  ..  -.  -.': '醒', '-.-.  ...': '吃', '....  .  ...': '喝',
  '..-.  .-..  -.': '饭', '-.-.  .-': '菜', '-.--  ..-  ...': '鱼', '-..-  ..  .-': '虾',
  '-..-  ..  .  ...': '蟹', '-...  .  ..  ...': '贝', '-..  .-..  -.': '蛋', '-.  .-': '奶',
  '---  ..  ..-': '油', '-.--  .-..  -.': '盐', '-  .-..  -.  -.': '糖', '.  ..  .-..  -.': '酱',
  '-.-.  ..-  ...': '醋', '.  ..  ..-': '酒', '-.-.  .-': '茶', '-.-  .-': '咖',
  '..-.  .  ..  ...': '啡', '--.  ..-  ---': '果', '--  ...': '汁', '-...  ..  -.  -.': '冰',
  '-..  ---  -.  -.': '冻', '-  ..  .-..  -.': '甜', '...  ..-  .-..  -.': '酸', '.-..  .-': '辣',
  '-..-  ..  .-..  -.': '咸', '-..  .-..  -.': '淡', '-.-.  ---  ..-': '臭', '--  .  ..  ...': '美',
  '-.-.  ..-  ---': '丑', '....  ..-  .-': '坏', '...  .  .-..  -.': '善', '.  ...': '恶',
  '--  --..  .  -.': '真', '.  ..  .-': '假', '--  .  -.  -.': '正', '-..-  ..  .-': '邪',
  '--.-  ..  -.  -.': '清', '--  ..-  ---': '浊', '--  ..  -.  -.': '明', '.-..  -.': '暗',
  '.-..  ..  .-..  -.': '亮', '-...  .-': '白', '....  .  ..  ...': '黑', '....  ---  -.  -.': '红',
  '.-..  ..-  ...': '绿', '-...  .-..  -.': '蓝', '....  ..-  .-..  -.': '黄', '--  ...': '紫',
  '---  .  -.  -.': '橙', '..-.  .  -.': '粉', '....  ..-  ..  ...': '灰', '--  ---  -.  -.': '棕',
  '-.--  ..  -.': '银', '-  ---  -.  -.': '铜', '-  ..  .  ...': '铁', '-..-  ..  ...': '锡',
  '--.-  ..  .-..  -.': '铅', '--  ..  -.': '锌', '....  ---  -.  -.': '汞', '.-..  ..-  ...': '硫',
  '-  .-..  -.': '碳', '-.  .  -..': '氮', '---  .-..  -.  -.': '氧', '--.-  ..  -.  -.': '氢',
  '-.-.  .-..  ..-': '氯', '-.-  .-': '钾', '-.-.  .-': '钙'
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

    // 逐个字符查找，优先查中文映射
    return chars.map(char => {
      const trimmedChar = char.trim();
      // 先查中文反向映射
      for (let morse in morseToChineseChar) {
        if (morse === trimmedChar) {
          return morseToChineseChar[morse];
        }
      }
      // 再查英文映射
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
