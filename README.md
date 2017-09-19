Web-store
=====================
Проект интернет-магазина разработан с целью изучения курса "Front-end" (HTML, CSS, bootstrap , JavaScipt и т.д)
Даный проект содержит сборщик проектов gulp

### Возможности сборщика gulp
1. Минификация css
2. Добавление вендорных префиксов в css
3. Автоматическое обновление браузера
4. Минификация и конкатенация JavaScript
5. Оптимизация картинок
6. Создание спрайтов


**Процесс установки.**

1. Клонируем репозиторий
```js
git clone https://github.com/kravc4enkoserg/Web-store.git
```
2. Перейдите в склонированную папку или откройте е в редакторе кода
```js
cd template
```

3. Разворачивание проекта (установка всех модулей). У вас должен быть установлен nodejs и gulp глобально
```js
 npm up
```
---
**Запуск gulp**

1. Запуск gulp. Запустится таск default
```js
 gulp
```
---
***Установка gulp глобально `(если он не установлен)`***
1. Установите nodejs по ссылке [Nodejs](https://nodejs.org/uk/)

2. Установите gulp глобально
```js
npm i gulp -g
```
---
***Привяжите к своему репозиторию***
1. Создайте новый репозиторий на github

2. Подвяжите текущий template к своему репозиторию
```js
git remote set-url origin "ссылка на ваш репозиторий"
```
---


**Структура папок**

Название папок  | Содержание файла
----------------|----------------------
app             | Директория с готовым проектом
app/css         | Готовые стили к продакшену
app/js          | Готовый js к продакшену
app/img         | Готовые картинки к продакшену
app/fonts       | Шрифты
src             | Директория с исходными файлыми
src/css         | Исходные стили, здесь мы пишем наши стили и они будут конвертироваться в app/css
src/img         | Исходные картинки, они будут минифицироваться и перегоняться в app/img
src/js          | Исходный js будет минифицироваться и переносится в app/js
src/sprite      | Папка для нарезанных картинок под будущие спрайты, после конветрации попадут в app/img

---
**Используемые модули**

```js
var gulp         = require('gulp'), // Подключаем Gulp
    browserSync  = require('browser-sync'), // Подключаем Browser Sync
    concat       = require('gulp-concat'), // Подключаем gulp-concat (для конкатенации файлов)
    uglify       = require('gulp-uglifyjs'), // Подключаем gulp-uglifyjs (для сжатия JS)
    cssnano      = require('gulp-cssnano'), // Подключаем пакет для минификации CSS
    rename       = require('gulp-rename'), // Подключаем библиотеку для переименования файлов
    imagemin     = require('gulp-imagemin'), // Подключаем библиотеку для работы с изображениями
    pngquant     = require('imagemin-pngquant'), // Подключаем библиотеку для работы с png
    cache        = require('gulp-cache'), // Подключаем библиотеку кеширования
    autoprefixer = require('gulp-autoprefixer'),// Подключаем библиотеку для автоматического добавления префиксов
    spritesmith = require('gulp.spritesmith'), // Подключение библиотеки для создания спрайтов
    merge = require('merge-stream');

```
**Все таски gulp file**


```js
var gulp         = require('gulp'), // Подключаем Gulp
browserSync  = require('browser-sync'), // Подключаем Browser Sync
concat       = require('gulp-concat'), // Подключаем gulp-concat (для конкатенации файлов)
uglify       = require('gulp-uglifyjs'), // Подключаем gulp-uglifyjs (для сжатия JS)
cssnano      = require('gulp-cssnano'), // Подключаем пакет для минификации CSS
rename       = require('gulp-rename'), // Подключаем библиотеку для переименования файлов
imagemin     = require('gulp-imagemin'), // Подключаем библиотеку для работы с изображениями
pngquant     = require('imagemin-pngquant'), // Подключаем библиотеку для работы с png
cache        = require('gulp-cache'), // Подключаем библиотеку кеширования
autoprefixer = require('gulp-autoprefixer'),// Подключаем библиотеку для автоматического добавления префиксов
spritesmith = require('gulp.spritesmith'), // Подключение библиотеки для создания спрайтов
merge = require('merge-stream');

gulp.task('css', function(){ // Создаем таск Sass
return gulp.src('src/css/**/*.css') // Берем источник
    .pipe(autoprefixer(['last 15 versions', '> 1%', 'ie 8', 'ie 7'], { cascade: true })) // Создаем префиксы
    .pipe(gulp.dest('app/css')) // Выгружаем результата в папку app/css
    .pipe(browserSync.reload({stream: true})) // Обновляем CSS на странице при изменении
});

gulp.task('browser-sync', function() { // Создаем таск browser-sync
browserSync({ // Выполняем browserSync
    server: { // Определяем параметры сервера
        baseDir: 'app' // Директория для сервера - app
    },
    notify: false // Отключаем уведомления
});
});

gulp.task('sprite', function () { // Создаем таск sprite
var spriteData = gulp.src('src/sprite/*.png').pipe(spritesmith({ // Настройка спрайта
    imgName: 'sprite.png',
    cssName: 'sprite.css',
    imgPath: '../img/sprite.png'
}));
// return spriteData.pipe(gulp.dest('app/img/')); // выгружаем спрайты в папку img
var imgStream = spriteData.img
    .pipe(gulp.dest('app/img/'));

var cssStream = spriteData.css
    .pipe(gulp.dest('src/css/'));

return merge(imgStream, cssStream);
});

gulp.task('scripts', function() {
return gulp.src('src/js/**/*.js')
    .pipe(concat('plugins.min.js')) // Собираем их в кучу в новом файле plugins.min.js
    .pipe(uglify()) // Сжимаем JS файл
    .pipe(gulp.dest('app/js')); // Выгружаем в папку app/js
});

gulp.task('css-libs', ['css'], function() {
return gulp.src('app/css/style.css') // Выбираем файл для минификации
    .pipe(cssnano()) // Сжимаем
    .pipe(rename({suffix: '.min'})) // Добавляем суффикс .min
    .pipe(gulp.dest('app/css')); // Выгружаем в папку app/css
});

gulp.task('img', function() {
return gulp.src('src/img/**/*') // Берем все изображения из app
    .pipe(cache(imagemin({  // Сжимаем их с наилучшими настройками с учетом кеширования
        interlaced: true,
        progressive: true,
        svgoPlugins: [{removeViewBox: false}],
        use: [pngquant()]
    })))
    .pipe(gulp.dest('app/img')); // Выгружаем на продакшен
});

gulp.task('watch', ['browser-sync', 'css', 'scripts', 'sprite', 'img'], function() {
gulp.watch('src/css/**/*.css', ['css']); // Наблюдение за css файлами в папке css
gulp.watch('src/sprite/*.png', ['sprite']); // Наблюдение за папкой с картинками для спрайтов  папке sprite
gulp.watch('src/js/**/*.js', ['scripts']);   // Наблюдение за JS файлами в папке js
gulp.watch('src/img/**/*.', ['img']);
gulp.watch('app/*.html', browserSync.reload); // Наблюдение за HTML файлами в корне проекта
gulp.watch('app/js/**/*.js', browserSync.reload);   // Наблюдение за JS файлами в папке js
});

gulp.task('clear', function () {
return cache.clearAll();
});

gulp.task('default', ['watch']);

```
---
**Используемые шрифты**

Harmonia_regular (шрифт используется по умолчанию);
Harmonia_black;
Harmonia_semibold;
Harmonia_bold;
Harmonia_italic.

---
**Используемые библиотеки, фреймверки**

Bootsrtap - фреймворк, используется сетка bootstrap, а также карусель bootstrap для инициализации карусели на главной страници;
Font-awesome - библиотека иконок;

---
**Используемые плагины**

Название плагина         | Описание плагина
-------------------------|----------------------
flexslider               | Плагин jQuery, который организует настраиваемый слайдер. Используется в качестве слайдера продуктов для страници product 
jquery.formstyler.theme  | Плагин jQuery, для стилизации чекбоксов, радиокнопок и числовых полей на страницах product и category 
slicknav                 | Плагин для jQuery, адаптивная мобильная кнопка меню, включается при использовании устройств с разрешением экрана меньше 991px
plagin                   | Написан плагин на Js который позволяет скрывать/показывать sidebar на странице category при использовании устройств с разрешением экрана меньше 991px

---
**Используемые дефолтные классы**

flex-container - задает блоку свойство display: flex;
aligh-center - вертикальное выравнивание элемента по центру;
justify-sp-between - горизонтальное выравнивание флекс-элементов: первый блок располагается в начале главной оси, последний блок – в конце, все остальные блоки равномерно распределены в оставшемся пространстве;
justify-sp-around - горизонтальное выравнивание флекс-элементов: все блоки равномерно распределены вдоль главной оси, разделяя все свободное пространство поровну;
justify-con-center - горизонтальное выравнивание флекс-элементов: блоки располагаются в центре главной оси;
flex-wrap - разрешает перенос флекс-элементов;
orange-text - задает тексту оранжевый цвет;
red-bg - задает фон элементу красный цвет;
black-bg - задает фон элементу красный черный цвет;
blue-bg -задает фон элементу синий цвет;
green-bg - задает фон элементу зеленый цвет;
white-bg - задает фон элементу белый цвет;
light-orange-bg - задает фон элементу светло-оранжевый цвет;
orange-bg - задает фон элементу оранжевый цвет;