# Guida-laravel
##  CREARE NUOVO PROGETTO

composer create-project --prefer-dist laravel/laravel:^9.2 your_project_name_here

1- Creare la carttella tramite comando da terminale: composer create-project --prefer-dist laravel/laravel:^9.2 your_project_name_here
2- aprire la cartella creata e basta su vs code
3- cliccare nella sidebar di sinistra su source controll
4- cliccare sul bottone blu per "pubblica su github"
5- scegliere pubblica repo con visibilità "pubblica"
6- ATTIVARE IL SERVER: terminata la pubblicazione su github, apire il terminale di vs code integrato o il terminale di sistema nella cartella progetto e lanciare il comando: php artisan serve
7- in un altro terminale aperto sulla cartella del progetto, lanciare : composer require pacificdev/laravel_9_preset
8 - poi lanciare il comando da terminale: php artisan preset:ui bootstrap
9 - lanciare il comando da terminale: npm install
10 - lanciare il comando da terminale per attivaare i secondo server da mantenere attivo durante il lavoro: npm run dev
11 - riavviare invece il server attivato con il comando: php artisan serve
12 - inserire nel <head></head> del layout questo codice per poter intepretare bootstrap con vite: @vite('resources/js/app.js')
Il layout diventerà:
   

     <head>
            ....
            @vite('resources/js/app.js')
        </head>

### AVVIARE SERVER

php artisan serve

### CREARE CONTROLLER

php artisan make:controller Nome controller

php artisan make:controller Nome controller --model=nomeModel (per avere un cotroller resources) -----> da importare in web.php con Route::resource('/', NomeController::class );

## CREARE MODEL

php artisan make: model nomeModel

## COMANDO DA TERMINALE PER VEDERE TUTTE LE ROUTE

php artisan route: list

## COMANDO PER MIGRARE LE TABELLE NELLE MIGRATIONS

php artisan migrate

##  COMANDO PER CREARE UNA MIGRATION

php artisan make:migration create_nometabella_table

## COMANDO PER ESEGUIRE UN SEEDER

php artisan db:seed --class=NomeTabellaSeeder

## COMANDO PER FARE UN ROLLBACK AD UN BACH MINORE

php artisan migrate:rollback

## COMANDO PER FARE ROLLBACK 0 E REFRESHARE LE TABELLE

php artisan migrate:fresh

## CONTROL P PER CERCARE I FILE 

## - COMANDO PER LA CREAZIONE DEI MODEL, CONTROLLER, TABELLA E SEEDER

php artisan make:model Train -mcs (controller base) o -mrs (controller con tutte le crud)

## - COMANDO PER REFRESHARE LE TABELLE E RILANCIARE ANCHE I SEEEDER

php artisan migrate:refresh --seed






## CLONARE REPO E INSTALLARE TUTTI I FILE MANCANTI

1. clonare la repo in locale
2. `composer install`
3. `npm install`
4. `cp .env.example .env` (fare una **copia** del file .env)
5. `php artisan key:generate`
6. creare un nuovo database con phpMyAdmin
7. inserire le credenziali del database nel file .env
8. se già avviato, riavviare artisan serve
9. `php artisan migrate`
10. `php artisan db:seed` (se ci sono seeder)

11.git clean -d -n nel caso ci siano errori di pull







## Divisione laravel frontend e backend

    - composer create-project laravel/laravel:^9.2 example-app
    - php require laravel/breeze --dev
    - php artisan breeze:install
    - composer require pacificdev/laravel_9_preset
    - php artisan preset:ui bootstrap --auth
    - npm i
    - npm run dev
    - compilare il file .env con le info del DB
    - Lanciare la prima: php artisan migrate
    - Spostiamo la rotta della dashboard all'interno del gruppo gestito dal middleware
    - Sposto la logica della funzione della pagina di dashboard nel controller: php artisan make:controller Admin/AdminController
    - Modificare il file RouteServiceProvider: public const HOME = '/admin';
    - Il gruppo di rotte gestite dal middleware devono essere tutte con il path http che inizia con: localhost:8000/admin..
        - Aggiungere il: ->prefix('nome del prefisso rotta')       Al Gruppo di rotte
        - Modifcare il path delle rotte interno al gruoppo se necessario 
        - Modificare i link associati ai bottoni preesistenti di laravel breeze 
    - Tutte le rotte devono avere anche il name() che inizia con admin.
        - Aggiungere la funzione ->name('admin.') Dopo il ->prefix('admin')

## Se vogliamo usare lo slug
    - Modificare le rotte aggiungendo ->parameters([ ....Dato parametro diverso da Id...... ])





## Se vogliamo usare un campo unique in una colonna e poi abbinare la modifica con crud
     - creare il file Request tramite: php artisan make:request UpdatePostRequest
     - Utilizzare il file all'interno del controller per update e store: public function update(UpdatePostRequest $request, Post $post)
     - Nel file request aggiornare il campo false di authorized in true
     - inseirre nella funzione rule() la validazione per ogni campo colonna da controllare
     - Importare la classe Rule nel file request: use Illuminate\Validation\Rule;
     - Scrivere questa funzione: public function rules()
    {
        return [
            'title' => [ 'required', 'max:20', Rule::unique('posts')->ignore($this->post) ],
            'content' => ['nullable']
        ];
    }




## Upload images
    - modificare il disks storage in config/fylesistems.php e nel .env per renderlo 'public'
    - Lanciamo il comando: php artisan storage:link
    - aggiornare migrations,models ($fillable) con la nuova colonna per le immagini path, e aggiornare la validazione 
    - aggiungee al form l'attributo enctype e il campo input type file con il name della colonna della migations
    - scrivere la logica per il controller nella update e store
##### caricamento dell'immagine se presente 
        if( $request->hasFile('cover_image') ){
            
            //PROCEDIMENTO SOLO SE SIAMO NELLA UPDATE!!!!!
            if( $post->cover_image ){
                Storage::delete($post->cover_image);
            }
            //FINE: PROCEDIMENTO SOLO SE SIAMO NELLA UPDATE!!!!!

            //Genere un path di dove verrà salvata l'iimagine nel progetto
            $path = Storage::disk('public')->put( 'post_images', $request->cover_image );

            $form_data['cover_image'] = $path;
        }



## cancellare l'immagine nella storage al cancellamento del records nella tabella
    - if( $post->cover_image ){
                Storage::delete($post->cover_image);
        }   


## Creazione tabella Many to Many (es: tabella Tag collegata alla Post)

	- Creare il model della tabella : php artisan make:model Admin/Tag -ms
	- Creare il controller resources : php artisan make:controller Admin/TagController -r
	- Aggiungere i campi della table nella create tags. 
	- Fare una migrazione per far comparire la tabella tags nel database.
	- Creare la cartella Pivot: php artisan make:migration create_post_tag_table 
	**(REGOLE da seguire nomi delle tabelle al singolare messi in ordine alfabetico collegate dall'underscore_ !!).**
	- Fare la migrate della tabella Pivot.
	
	- La tabella pivot dev'essere priva per convenzione delle colonne id e timestamps quindi cancellarle.
	
	- Creare le due colonne dei due id nella table pivot:

		$table->unsignedBigInteger('post_id')
		$table->foreign('post_id')->references('id')->on('posts')->cascadeOnDelete();

		$table->unsignedBigInteger('tag_id')
		$table->foreign('tag_id')->references('id')->on('tags')->cascadeOnDelete();

		$table->primary(['post_id','tag_id'])

	- Modificare il model della tabella Tags aggiungendo la protected table, la protected fillable e la public function posts(){
			return $this->belongsToMany(Post::class);

	- Modificare il model della table Posts aggiungendo la public function tags(){
			return $this->belongsToMany(tag::class);

	- Riempire il seeder della table Tags per popolarla.

	- Per comoditá aggiungere il seeder della table Tags nel DatabaseSeeder.php

	- Per mostrare le informazioni della table Tags bisogna fare un ciclo con una condizione:

    @if( $post->tags )
    	@foreach ( $post->tags as $elem )
    		<div> {{ $elem->name }} </div>
    	@endforeach
    @endif


	- Per poter ciclare i tag in un div checkbox andare nella create del controller posts e scrivere $tag = Tag::all() e passarlo nel compact della view.

	- Nella validazione mettere al campo tags l'exist.

	- Nel form creare il div ciclato con la checkbox

	- Nella create bisogna creare una condizione dopo la creazione del record

    if( $request->has('tags') ){
    	$singolo_post->tags()->attach($request->tags);

	- Nella edit scrivere $tag = Tag::all() e passarlo nel compact della view.

	- Nel form edit copiare e incollare il div della checkbox messo nella create e creare il ternario per determinare se la singola check é giá spuntata o checked

	@if ( $errors->any() )

	{{ in_array( $elem->id, old( 'tags', [] ) ) ? 'checked' : '' }}

	@else

	{{ ( $post->tags->contains($elem) ) ? 'checked' : '' }}

	- Aggiornare la validazione dell'edit

	- Nell'update scrivere l'altra condizione dopo l'update del record

	if( $request->has('tags') ){
	
	$singolo_post->tags()->sync($request->tags);

	- Nella destroy abbinare un array vuoto nella pivot alla distruzione del post

	$post->tags()->sync( [] );

