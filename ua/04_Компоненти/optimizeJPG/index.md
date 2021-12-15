
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<h3>Плаігн оптимізації jpg зображень Modx Evo </h3>
Плаігн оптимізації jpg зображень.
<p>Сервіс гугл PageSpeed Insights рекомендує «проводити базову і розширену оптимізацію всіх зображень… При розширеній оптимізації проводиться сжимання файлів JPEG і PNG (без втрат).»</p>
<p>PNG, які використовуються в верстці, я сжимаю сервісом tinypng, а ось про сжимання JPG якось не задумувався. В PageSpeed з тієї причини написано: «Для файлів JPEG рекомендується використовувати jpegtran чи jpegoptim (доступно тільки для Linux, виконувати з властивістю --strip-all).»</p>
<p>На хостингу знайшовся jpegtran, і я швидко зробив такий плагін:</p>
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
<p>Для работи плагіна потрібно присутність на сервері jpegtran і права на виконування функції exec.</p>
<p>Плагін виконує оптимізацію при загрузці зображень через KCFinder, управління файлами, SimpleGallery (для неї можна також оптимізувати уже завантаженні зображення).</p>
