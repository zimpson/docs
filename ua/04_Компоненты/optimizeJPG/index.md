
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<h3>Плагін оптимізації jpg зображень Modx Evo </h3>
Плагін оптимізації  jpg зображень.
<p>Гугл сервіс PageSpeed Insights рекомендує «проводити базову і розширенну оптимізацію всіх зображень… При розширенній оптимизації проводиться сжимання файлів JPEG і PNG (без втрат).»</p>
<p>PNG, які використовуються в верстці, я сжимаю сервісом tinypng, а ось про сжимання JPG якось не задумувався. В PageSpeed по цьому поводу написано: «Для файлів JPEG рекомендуєтся використовувати jpegtran чи jpegoptim (доступно тільки для Linux, виконувати з параметром --strip-all).»</p>
<p>На хостингу знайшовся jpegtran, і я швиденько зробив такий плагін:</p>
<pre class="brush: php;">
$e = &$modx->event;
if (!function_exists('optimizeJPG')) {
    function optimizeJPG($file) {
        $ext = strtolower(end(explode('.', $file)));
        if ($ext == 'jpeg' || $ext == 'jpg') {
            $cmd = '/usr/bin/jpegtran -optimize -progressive -copy none -outfile '.escapeshellarg($file.'_').' '.escapeshellarg($file);
            exec($cmd, $result, $return_var);
            @rename($file.'_',$file);   
        }
    }
}
if ($e->name == "OnFileBrowserUpload" || $e->name == "OnFileManagerUpload") {
    optimizeJPG($filepath.'/'.$filename);
}
if ($e->name == "OnSimpleGalleryRefresh") {
    optimizeJPG(MODX_BASE_PATH.$sg_image);
}
</pre>
<p>Для работи плагіна потрібно наявність на сервері jpegtran і права на виконання функції exec.</p>
<p>Плагін виконує оптимізацію при загрузці зображень через KCFinder, управління файлами, SimpleGallery (для неї можна також оптимізувати уже завантаженні зображення).</p>
