# Настройка php-cs-fixer 

Ставим либы:

`composer require --dev friendsofphp/php-cs-fixer`

`composer require --dev squizlabs/php_codesniffer`

### Скопируйте данный код в `.git/hooks/pre-commit` - проверено на Linux(Ubuntu 19.10), на MAC должно тоже работать норм. 
Дать права на исполнение, для линукса `chmod +x .git/hooks/pre-commit`

```php

#!/bin/bash

echoerr() { echo -e "\e[1;31m$@\e[0m" 1>&2; }

git diff --diff-filter=MAT --name-only --cached | while read line;
do
	echo "php-cs-fixer pre commit hook start"
	git add .

        PHP_CS_FIXER="./vendor/bin/php-cs-fixer"
        PHP_CS_CONFIG=".php_cs"
        CHANGED_FILES=$(git diff --cached --name-only --diff-filter=ACM -- '*.php')

        if [ -n "$CHANGED_FILES" ]; then
	  echo "Измененные файлы:"
	  echo $CHANGED_FILES

          $PHP_CS_FIXER fix --allow-risky=yes --config="$PHP_CS_CONFIG" $CHANGED_FILES;
          git add $CHANGED_FILES;
        fi
    
       echo "php-cs-fixer pre commit hook finish"
       # print more information about cs
       ./vendor/bin/phpcs --report=code --encoding=utf-8 --extensions=php --ignore=Tests $line

done



```

### В корень проекта положите файл `.php_cs` c таким содержимым:

```
<?php

$finder = PhpCsFixer\Finder::create()
    ->exclude(['assets', 'docker-local', 'node_modules', 'docs', 'tmp-docker', 'vendor'])
    ->notPath('*')
    ->in(__DIR__);

return PhpCsFixer\Config::create()
    ->setRules([
        'array_syntax' => ['syntax' => 'short'],
        'blank_line_after_namespace' => true,
        'blank_line_before_return' => true,
        'linebreak_after_opening_tag' => true,
        'no_extra_blank_lines' => true,
        'no_trailing_whitespace' => true,
        'no_unused_imports' => true,
        'no_useless_else' => true,
        'no_useless_return' => true,
        'phpdoc_order' => true,
        'phpdoc_separation' => true,
        'phpdoc_single_line_var_spacing' => true,
        'phpdoc_trim' => true,
        'single_blank_line_at_eof' => true,
        'single_blank_line_before_namespace' => true,
        '@Symfony' => true,
        '@Symfony:risky' => true,
        '@PHP71Migration' => true,
        'dir_constant' => true,
        'heredoc_to_nowdoc' => true,
        'modernize_types_casting' => true,
        'no_multiline_whitespace_before_semicolons' => true,
        'no_unreachable_default_argument_value' => true,
        'ordered_class_elements' => true,
        'ordered_imports' => true,
        'phpdoc_add_missing_param_annotation' => ['only_untyped' => false],
        'declare_strict_types' => true,
        'doctrine_annotation_braces' => true,
        'doctrine_annotation_indentation' => true,
        'doctrine_annotation_spaces' => true,
        'psr4' => true,
        'no_php4_constructor' => true,
        'no_short_echo_tag' => true,
        'semicolon_after_instruction' => true,
        'align_multiline_comment' => true,
        'doctrine_annotation_array_assignment' => true,
        'general_phpdoc_annotation_remove' => ['annotations' => ["author", "package"]],
        'list_syntax' => ["syntax" => "short"],
        'phpdoc_types_order' => ['null_adjustment'=> 'always_last'],
        'single_line_comment_style' => true,
    ])
    ->setCacheFile(__DIR__.'/.php_cs.cache')
    ->setFinder($finder);
```
#### Добавить в .gitignore `.php_cs.cache`

### Результат в консоли:
```
└─▸  git commit -m 'traceway-devel - merge dev into dev'
php-cs-fixer pre commit hook start
Измененные файлы:
path/to/File1.php path/to/File2.php

Loaded config default from ".php_cs".
Using cache file "/var/www/{project-name}/php-backend/.php_cs.cache".
Paths from configuration file have been overridden by paths provided as command arguments.
   1) commands/{filename1}.php
   2) commands/{filename2}.php
   

Fixed all files in 13.908 seconds, 38.000 MB memory used
php-cs-fixer pre commit hook finish
```
