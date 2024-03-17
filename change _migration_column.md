# Como utilizar o método [change](https://laravel.com/docs/11.x/migrations#modifying-columns) nas migrations no Laravel 11

## Introdução

Durante o desenvolvimento de uma aplicação em Laravel, é comum a necessidade de alterar dados das colunas em nossa base de dados. Felizmente, o Laravel oferece uma maneira simples de realizar essas mudanças usando o método `change`. Neste artigo, vamos explorar o uso desse método na versão 11 do Laravel.

## Criando uma migração de exemplo

Para ilustrar o processo, vamos criar uma migração chamada create_books_table para representar uma tabela em nosso sistema. Primeiramente, execute o seguinte comando em seu ambiente:

``` php
php artisan make:migration create_books_table --table=books
```

Após a execução deste comando, teremos criado o arquivo de migração. Nele, vamos definir a estrutura da nossa tabela conforme abaixo:

```php

<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('books', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('description', 100)->nullable();
            $table->string('isbn', 10);
            $table->integer('total_pages');
            $table->decimal('original_price', 6, 2);
            $table->integer('quantity')->nullable();
            $table->timestamps();
            $table->engine = 'InnoDB';
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('books');
    }
};

```

Com a migração criada, podemos executar `php artisan migrate`. Se tudo ocorrer conforme o esperado, nossa tabela será criada com sucesso.

## Utilizando o método change

Após a criação da tabela, vamos imaginar que precisamos fazer algumas alterações:

- A coluna isbn precisa ter tamanho 17 em vez de 10.
- A coluna descrição deve ser do tipo text em vez de varchar.

Para corrigir isso, vamos criar uma nova migração chamada `modify_books_table`:

```php
php artisan make:migration modify_books_table --table=books
```
Em seguida, vamos organizar a migração da seguinte forma:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::table('books', function (Blueprint $table) {
            $table->text('description')->nullable()->change();
            $table->string('isbn', 17)->change();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::table('books', function (Blueprint $table) {
            $table->string('description', 100)->nullable()->change();
            $table->string('isbn', 10)->change();
        });
    }
};

```

> Ao criar migrações no Laravel é importante implementar tanto o método up() quanto o método down(). Enquanto o método up() aplica as alterações no esquema do banco de dados, o método down() permite reverter essas alterações de forma segura, caso necessário. Isso garante a capacidade de desfazer mudanças e contribui para a estabilidade e flexibilidade do desenvolvimento do projeto.

Como podemos observar, o processo é simples. Basta definir a nova estrutura da coluna e, em seguida, chamar o método `change`. Com isso, ao executar `php artisan migrate`, as alterações serão aplicadas.

## Pontos importantes

### Colunas suportadas

Não é possível alterar todos os tipos de colunas no Laravel usando o método `change`. Por exemplo, não é possível modificar uma coluna do tipo ENUM adicionando um novo valor. Nestes casos, consultas SQL diretas usando a class `DB` podem ser necessárias.

### Versão do Laravel

A partir da versão 11 do Laravel, é necessário repetir toda a estrutura da coluna no momento da alteração como fizemos. Nas versões anteriores, apenas os novos atributos precisavam ser informados. Consulte o guia de [atualização do Laravel](https://laravel.com/docs/11.x/upgrade#modifying-columns) para mais detalhes.

### Doctrine/DBAL

Se estiver usando uma versão muito antiga do Laravel, pode ser necessário instalar a dependência do DBAL para que o `change` funcione. O doctrine/dbal é uma biblioteca de banco de dados que fornece uma camada de abstração sobre diferentes sistemas de gerenciamento de banco de dados em PHP. Execute o seguinte comando para instalar a dependência no seu projeto, se necessário: 

```php
composer require doctrine/dbal
```

## Conclusão

Neste artigo, exploramos o fluxo de alteração de colunas no Laravel usando o método change. Discutimos o processo, as diferenças nas versões do framework e a necessidade de alterações manuais em casos não suportados pelo método change.

Agora você está pronto para modificar as colunas em sua aplicação Laravel conforme necessário.










