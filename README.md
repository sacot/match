# match
<?php
//ini_set('display_errors', 0);
session_start();
$name = $_SESSION['Student_name'];
define('max_view',4);
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./css/index.css">
    <link href="https://fonts.googleapis.com/css?family=Amatic+SC:700 rel="stylesheet">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Hachi+Maru+Pop&display=swap" rel="stylesheet">
    <script src="./js/jquery/modernizr.custom.min.js"></script>
    <script src="./js/jquery/jquery-1.10.2.min.js"></script>
    <script src="./js/jquery/jquery.ba-throttle-debounce.min.js"></script>
    <script src="./js/index1.js"></script>
    <title>TOPぺーじ</title>
</head>
<body>
<?php
$res = null;
$pdo = null;
$ccount = null;
$sstart = 1;
$count = 6;
try{	
    $pdo = new PDO('mysql:host=localhost;dbname=js2019_matching;charset = utf8','sakota','sakota');
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    $pdo->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
 }catch (PDOException $Exception){
    die('接続エラー：'.$Exception->getMessage());
 }
    //$sql = ("SELECT * FROM student ORDER BY Student_id LIMIT 1,4");
    //$res = $pdo -> query($sql);
    $stmt = $pdo->prepare("SELECT COUNT(*) AS count FROM student");
    $stmt->execute();
    $ccount = $stmt->fetchColumn();
    $pages = ceil($ccount / max_view);
    echo $pages;

    if(!isset($_GET['page_id'])){
        $now = 1;
    }else{
        $now = $_GET['page_id'];
    }
    $sql = ("SELECT * FROM student ORDER BY Student_id LIMIT :start,:max");
    $res = $pdo -> query($sql);

    if($now == 1){
        $res->bindValue(":start",$now -1,PDO::PARAM_INT);
        $res->bindValue(":max",max_view,PDO::PARAM_INT);
    }else{
        $res->bindValue(":start",($now -1) * max_view,PDO::PARAM_INT);
        $res->bindValue(":max",max_view,PDO::PARAM_INT);
    }
    $res->execute();
    $data = $res->fetchAll(PDO::FETCH_ASSOC);

?>    
<header class="page-header" role="banner">
    <div class="inner clearfix">
        <h1 class="title">title</h1>
        <nav class="primary-nav" role="navigation">
            <ul>
                <li><a href="./search.php">学生検索</a></li>
                <li><a href="./vac/vaccinationEntry.php">ワクチン登録</a></li>
                <li><a href="./laf/index.php">落とし物検索</a></li>
                <li><a href="./student_updateform.php">プロフィール編集</a></li>
                <li><a href="./logout.php">ログアウト</a></li>
                <li id="name">ようこそ'<?php echo $name;?>'さん</li>
            </ul>
        </nav>
    </div>
</header>
    <div class="container">
<?php
    for($i = $start; $i < $count; $i++){
        foreach($res as $value){
            $Student_id = $value['Student_id'];
?>
    <div class="main-container">
            <div class="image-content">
                <?php
                 if($value['Image'] != NULL){?>
                    <img src="<?php echo $value['Image'];?>">
                 <?php }else{?>
                    <img src="./images/anabuki.png">
                 <?php }?>
            </div>
        
        <div class="user-container">
            <div class="name-content">
                <ul>
                    <li><?php echo "<a href="."detail.php?Student_id=$Student_id".">".$value['Student_name']."</a>";?></li>
                    <li><?php echo $value['Gender'];?></li>
                    <li><?php echo $value['Hobby'];?></li>
                    <!--<li><?php echo $value['Sns'];?></li>-->
                </ul>
            </div>
        </div>
            <div class="text-container">
                <?php echo $value['Comment'];?>
            </div>
    </div>
            
<?php
    }
}

for($i = 1; $i <= $pages; $i++){
    if($i == $now){
        echo $now;
    }else{
        echo "<a href='./index1.php?page_id=$i'>$i</a>";
    }
}
?>
</div>
</body>
</html>
