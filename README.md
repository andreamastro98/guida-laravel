# GUIDA LARAVEL BASATA SU UNA LOCALHOST BACKEND CHE PERMETTE LA CREAZIONE DI POST CON CATEGORIE E TAG ANNESSI
##  CREARE NUOVO PROGETTO

    - composer create-project --prefer-dist laravel/laravel:^9.2 your_project_name_here

    - Creare la carttella tramite comando da terminale: composer create-project --prefer-dist laravel/laravel:^9.2 your_project_name_here
    - aprire la cartella creata e basta su vs code
    - cliccare nella sidebar di sinistra su source controll
    - cliccare sul bottone blu per "pubblica su github"
    - scegliere pubblica repo con visibilità "pubblica"
    - ATTIVARE IL SERVER: terminata la pubblicazione su github, apire il terminale di vs code integrato o il terminale di sistema nella cartella progetto e lanciare il comando: php artisan serve
    - in un altro terminale aperto sulla cartella del progetto, lanciare : composer require pacificdev/laravel_9_preset
    - poi lanciare il comando da terminale: php artisan preset:ui bootstrap
    - lanciare il comando da terminale: npm install
    - lanciare il comando da terminale per attivare i secondo server da mantenere attivo durante il lavoro: npm run dev
    - riavviare invece il server attivato con il comando: php artisan serve
    - inserire nel <head></head> del layout questo codice per poter intepretare bootstrap con vite: @vite('resources/js/app.js')
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

## COMANDO PER LA CREAZIONE DEI MODEL, CONTROLLER, TABELLA E SEEDER

    php artisan make:model Train -mcs (controller base) o -mrs (controller con tutte le crud)

## COMANDO PER REFRESHARE LE TABELLE E RILANCIARE ANCHE I SEEEDER

    - Aggiungere 

    php artisan migrate:refresh --seed






## CLONARE REPO E INSTALLARE TUTTI I FILE MANCANTI

    - clonare la repo in locale
    - `composer install`
    - `npm install`
    - `cp .env.example .env` (fare una **copia** del file .env)
    - `php artisan key:generate`
    - creare un nuovo database con phpMyAdmin
    - inserire le credenziali del database nel file .env
    - se già avviato, riavviare artisan serve
    - `php artisan migrate`
    -  `php artisan db:seed` (se ci sono seeder)

    - git clean -d -n nel caso ci siano errori di pull







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




### caricamento dell'immagine se presente 
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





## INTEGRAZIONE VUE CON LARAVEL

    - Scrivere in api.php una route:get('/posts',[PostController::class, 'index']);


    - Creare un Controller base con una
    public function index(){
	    $posts = Post::all();

    //oppure per avere anche le relazioni

	    $posts = Post::with('category','tags')->get();

    //oppure per avere tot(3) elementi per pagina 

	    $posts = Post::with('category','tags')->paginate(3);

	    return response()->json([
	    	'success' => true,
	    	'posts' => $posts
	    ]);
    }

    - Clonare un template vue con tutto integrato nell'area di lavoro di vs code
    - Aprirci un terminale e fare 'npm i' per installare i node modules e 'npm run dev'. Avremo cosí 3 terminali di laravel e 1 di vite con tutti i server funzionanti.

    - Tramite struttura vue fare una chiamata axios al link del file api.
    - Ciclare l'array ottenuto dalla chiamata axios per mostrarlo in pagina.




## VUE ROUTER

    - npm install vue-router@4
    - nella cartella src inseriamo il file router.js
    - Scrivere nel file router.js:
    import { createRouter, createWebHistory } from 'vue-router';
    import AppHome from './pages/AppHome.vue';
    import PostList from './pages/PostList.vue';
    const router = createRouter({
    history: createWebHistory(),
    routes: [
    {
    path: '/',
    name: 'home',
    component: AppHome
    },
    {
    path: '/blog',
    name: 'posts',
    component: PostList
    },
    ]
    });
    export { router };

    - Nel file Main.js bisogna importare il router:
    import { router } from './router';




## SHOW LATO API

    - in api.php scrivere la nuova rotta show 
    	Route::get('/posts/{slug}',[PostController::class, 'show']);
    - nel controller scrivere una nuova public function show($slug){
    	$post = Post::with( 'category','tags' )-> where('slug', $slug )->first();

    if ($post){
    	return response()->json([
    		'success' => true,
    		'post' => $post
    	]);
    } else {
    	return response()->json([
    		'success' => false,
    		'error' => 'non ci sono posts'
    		]);
    	}
    }

    - Nella pages SinglePost scrivere 
    axios.get(`${this.apiUrl}/api/posts/${this.$route.params.slug}`).then((response) => {
    if (response.data.success) {
    this.post = response.data.post;
    } else {
    // redirect alla pagina 404
    this.$router.push({ name: 'not-found' })
    }
    });





## FILTRARE PER CATEGORY I POST

    - Creare un controller nella cartella api php artisan make:controller Api/CategoryController

    - Determinare la rotta del controller creato Route::get('/categories', [CategoryController::class,'index']);

    -scrivere nel controller una public function index(){
    	$categories = Category::all();

    	return response()->json(
    		[
    			'success' => true,
    			'categories' => $categories
    		]
    	);
    }

    - Aggiungere un method con l'altra chiamata axios:
    getCategories(){
    	axios.get(`${this.baseUrl}/api/categories`).then(res => {
    		this.categories = res.data.categories
    	})
    }

    - Scrivere la selected che cicla le categories con il v-model abbinato e l'@change con la funzione della chiamata axios principale e il value alle option.

    - Andare a modificare l'index del PostController aggiungendoci come parametri (Request $request) con una condizione

    if ($request->has('type_id')) {
    
    		$projects = Project::with('type', 'technologies')->where('type_id', $request->type_id)->paginate(3);

    	} else {

    		$projects = Project::with('type', 'technologies')->paginate(3);

    	}

    - Nel methods della chiamata axios principale del post scrivere la condizione che aggiunge il type_id al parametro

    const params = {
            page: projectApiPage
          }

          if(this.selectedType !=='all'){
            params.type_id = this.selectedType
          }





## FILTRARE PER TAGS I POST

    - php artisan make:controller Api/TagController --model=Admin/Tag
    - $tags = Tag::all();
    - return response()->json(
    		[
    			'success' => true,
    			'tags' => $tags
    		]
    	);

    - In api.php aggiungere il nuovo controller

    - aggiungere in FrontEnd: una variabile tags settata a null e una selectedTags:[]

    - Realizzare nuovo metodo GetTags() con la nuova chiamata axios che pusha l'informazione in tags.

    - Aggiungere lato html una checkbox ciclata per tanti elementi quanti quelli recuperati dalla chiamata api. con :value=elem.id e v-model=selectedTags

    - Scrivere nella watch: {
    	selectedTags:{
    		handler: 'getPosts',
    		deep: true	
    	}
    },

    - Aggiungere nella funzione getPosts la condizione 
    	if ( this.selectedTags.lenght > 0 ){
    	params.tags_ids = this.selectedTags.join(',')
    }

    - Tornare al PostController escrivere una variabille $query = Post::with(['category','tags']);

    if( $request->has( 'category_id' ) ){
    	$query->where( 'category_id', $request->category_id );
    }

    if( $request->has( 'tags_ids' ) ){
    	$tagIds = explode( ',', $request->tags_ids );
    	$query->whereHas('tags',function($query) use ($tagIds)
    	{
    		$query->whereIn('id',$tagIds);
    	});
    }

    $post = $query->paginate(3);

    return response()->json([
    	'success' => true,
    	'posts' => $posts
    ]);





## MAIL

    - php artisan make:model Admin/Lead -m
    - impostiamo nel model la protected table 'leads' e il fillable
    - Aggiungere le colonne name, email e message alla migration della table.
    - php artisan make:mail NewContact
    - in NewContact.php imposto una public $lead;
    - Nel costruttore impostiamo $this->lead = $_lead
    - Nell'envelope impostiamo:
    	replyTo: $this->lead->address,
    	subject:'Nuovo Contatto'
    - nel content, nella view: 'emails.new-contact-mail',
    - Nella cartella view dobbiamo creare la cartella Emails e generare dentro la page new-contact-mail.blade.php
    - scrivere l'html tipo di una mail ricevuta con {{ $lead->name }}, {{ $lead->email }}, {{ $lead->message }}
    - - php artisan make:controller Api/LeadController --model=Admin/Lead
    - importare il model Lead nel controller appna creato
    - impostare una public function store(Request $request){
    	$data = $request->all();
    
    	$new_lead = new Lead($data);
    	$new_lead->fill($data);
    	$new_lead->create();

    	//oppure

    	$new_lead = Lead::create($data);

    	Mail::to('no-reply@boolpress.it')->send(new NewContact( $new_lead ) );

    	return response()->json(
    		[
    			'success' => true
    		]
    	)	
    }

    - aggiungere in api.php la rotta del lead controller
    - copiare nel .env le credenziali di MailTrap
    - Nei data della pagina vue del form o del component dobbiamo passare come dati il name:'', email:'', message:'' e baseUrl
    - <form @submit.prevent="sendForm()">
    - Scrivere il form con i vari campi
    - SendForm(){
    	const data = {
    		name:this.name,
    		email:this.email,
    		message:this.message,
    	}

    	axios.post(`${this.baseUrl}/api/contacts`, data ).then( res => 	{
    		this.success = res.data.success
    		if ( this.success ){
    			this.name = '';
    			this.email = '';
    			this.message = '';
    		}
    	} )
    }