```pinescript```

```pinescript

//@version=6
indicator('Rlytrue RSI Alerts', overlay=false)

// Настройки RSI
rsiLength = input(14, 'Период RSI')
overbought = input(70, 'Уровень перекупленности')
oversold = input(30, 'Уровень перепроданности')
middleLine = input(50, 'Средняя линия')

// Расчет RSI
rsiValue = ta.rsi(close, rsiLength)

// Нормализация значений для плавного градиента
greenIntensity = math.min(100, math.max(10, (rsiValue - overbought) / (100 - overbought) * 100))
redIntensity = math.min(100, math.max(20, (oversold - rsiValue) / oversold * 100))

// Цвет фона с плавным градиентом
bgColor = if rsiValue >= overbought
    color.new(#66ff00, 100 - greenIntensity)
else if rsiValue <= oversold
    color.new(#FF3636, 100 - redIntensity)
else
    color.new(color.gray, 100) // transparent

// Отрисовка
plot(rsiValue, 'RSI', color=#ffff00, linewidth=1)
hline(overbought, 'Overbought', color=#66ff00)
hline(oversold, 'Oversold', color=#FF3636)
hline(middleLine, 'Middle Line', color=color.rgb(255, 255, 255, 44))
bgcolor(bgColor)

// Отображение текущего значения RSI на графике
var table rsiTable = table.new(position.top_right, 1, 1)
if barstate.islast
    table.cell(rsiTable, 0, 0, "RSI: " + str.tostring(rsiValue, "#"), bgcolor=bgColor, text_color=color.white, text_size=26)

// Уведомления
if rsiValue >= overbought
    alert('RSI Перекупленность (>70)! Текущее значение: ' + str.tostring(rsiValue), alert.freq_once_per_bar)
if rsiValue <= oversold
    alert('RSI Перепроданность (<30)! Текущее значение: ' + str.tostring(rsiValue), alert.freq_once_per_bar)
```

#### new

```pinescript
//@version=6
indicator('Rlytrue RSI Alerts + Volume', overlay=false)

// === Настройки RSI ===
rsiLength   = input(14, 'Период RSI')
overbought  = input(70, 'Уровень перекупленности')
oversold    = input(30, 'Уровень перепроданности')
middleLine  = input(50, 'Средняя линия')

// === Настройки Volume ===
showVolume  = input.bool(true, 'Показывать объёмы')
volHeight   = input.float(30, 'Высота объёма (% от шкалы)', minval=5, maxval=100, step=5) / 100
volOpacity  = input.int(85, 'Прозрачность объёма (0-100)', minval=0, maxval=100)
volColorUp  = input.color(#26a69a, 'Цвет объёма (рост)')
volColorDn  = input.color(#ef5350, 'Цвет объёма (падение)')

// === Расчёт RSI ===
rsiValue = ta.rsi(close, rsiLength)

// Нормализация значений для плавного градиента
greenIntensity = math.min(100, math.max(10, (rsiValue - overbought) / (100 - overbought) * 100))
redIntensity   = math.min(100, math.max(20, (oversold - rsiValue) / oversold * 100))

// Цвет фона
bgColor = if rsiValue >= overbought
    color.new(#66ff00, 100 - greenIntensity)
else if rsiValue <= oversold
    color.new(#FF3636, 100 - redIntensity)
else
    color.new(color.gray, 100)

// === Расчёт объёма (нормализация 0..volHeight*100) ===
volMax    = ta.highest(volume, 100)
volScaled = volMax > 0 ? (volume / volMax) * 100 * volHeight : 0

volBarColor = close >= open ? color.new(volColorUp, volOpacity) : color.new(volColorDn, volOpacity)

// === Отрисовка ===
// Объёмы — столбики у нижней границы панели
plot(showVolume ? volScaled : na, 'Volume (scaled)', style=plot.style_columns, color=volBarColor, display=display.pane, editable=false)

// RSI поверх объёмов
plot(rsiValue, 'RSI', color=#ffff00, linewidth=1)
hline(overbought, 'Overbought', color=#66ff00)
hline(oversold,  'Oversold',  color=#FF3636)
hline(middleLine, 'Middle Line', color=color.rgb(255, 255, 255, 44))
bgcolor(bgColor)

// === Таблица RSI ===
var table rsiTable = table.new(position.top_right, 1, 1)
if barstate.islast
    table.cell(rsiTable, 0, 0, "RSI: " + str.tostring(rsiValue, "#"), bgcolor=bgColor, text_color=color.white, text_size=26)

// === Уведомления ===
if rsiValue >= overbought
    alert('RSI Перекупленность (>70)! Текущее значение: ' + str.tostring(rsiValue), alert.freq_once_per_bar)
if rsiValue <= oversold
    alert('RSI Перепроданность (<30)! Текущее значение: ' + str.tostring(rsiValue), alert.freq_once_per_bar)
```
