# 🐘 PHP & Laravel — Questions d'Entretien (Français)

> Collection complète de questions d'entretien technique en français couvrant PHP, Laravel, Eloquent ORM, MVC, API REST, Artisan CLI, PHPUnit, MySQL et les bonnes pratiques de développement.

---

## 📋 Table des Matières

1. [PHP Fondamentaux](#1--php-fondamentaux)
2. [Orienté Objet (POO)](#2--orienté-objet-poo)
3. [Laravel — Architecture & Cycle de vie](#3--laravel--architecture--cycle-de-vie)
4. [Routing & Middleware](#4--routing--middleware)
5. [Eloquent ORM](#5--eloquent-orm)
6. [MVC & Blade](#6--mvc--blade)
7. [API REST avec Laravel](#7--api-rest-avec-laravel)
8. [Artisan CLI](#8--artisan-cli)
9. [PHPUnit & Tests](#9--phpunit--tests)
10. [MySQL & Base de Données](#10--mysql--base-de-données)
11. [Sécurité](#11--sécurité)
12. [Performance & Optimisation](#12--performance--optimisation)
13. [Bonnes Pratiques & Patterns](#13--bonnes-pratiques--patterns)

---

## 1 — PHP Fondamentaux

**Q1. Quelle est la différence entre `==` et `===` en PHP ?**

`==` compare les valeurs après conversion de type (loose comparison), tandis que `===` compare à la fois la valeur et le type (strict comparison).

```php
0 == "foo"   // true  (PHP convertit "foo" en 0)
0 === "foo"  // false (types différents)
```

---

**Q2. Qu'est-ce que le type juggling en PHP ? Donnez un exemple problématique.**

Le type juggling est la conversion automatique de types par PHP lors de comparaisons non strictes. Exemple classique de piège :

```php
// Avant PHP 8
"0" == false  // true
"0" == null   // false — incohérent !
0 == null     // true

// PHP 8 corrige beaucoup de ces cas
0 == "foo"    // false en PHP 8 (était true en PHP 7)
```

Toujours utiliser `===` dans les conditions critiques.

---

**Q3. Expliquez la différence entre `include`, `require`, `include_once` et `require_once`.**

| Directive | Erreur si absent | Duplication |
|---|---|---|
| `include` | Warning (continue) | Oui |
| `require` | Fatal Error (arrêt) | Oui |
| `include_once` | Warning (continue) | Non |
| `require_once` | Fatal Error (arrêt) | Non |

Utiliser `require_once` pour les classes et fichiers critiques.

---

**Q4. Qu'est-ce qu'un trait en PHP ? En quoi diffère-t-il d'une interface ou d'une classe abstraite ?**

Un **trait** est un mécanisme de réutilisation de code horizontal. Il peut contenir des méthodes avec implémentation, mais ne peut pas être instancié.

```php
trait Horodatable {
    public function getCreatedAtFormatted(): string {
        return $this->created_at->format('d/m/Y');
    }
}

class Article {
    use Horodatable;
}
```

| | Trait | Interface | Classe abstraite |
|---|---|---|---|
| Instanciable | ❌ | ❌ | ❌ |
| Implémentation | ✅ | ❌ | ✅ (partielle) |
| Héritage multiple | ✅ | ✅ | ❌ |
| Contrat | ❌ | ✅ | ✅ |

---

**Q5. Différence entre `array_map`, `array_filter` et `array_walk` ?**

```php
$nombres = [1, 2, 3, 4, 5];

// array_map : transforme chaque élément, retourne un nouveau tableau
$doubles = array_map(fn($n) => $n * 2, $nombres); // [2,4,6,8,10]

// array_filter : filtre les éléments, préserve les clés
$pairs = array_filter($nombres, fn($n) => $n % 2 === 0); // [1=>2, 3=>4]

// array_walk : modifie en place, passe clé + valeur
array_walk($nombres, function(&$val, $key) { $val += $key; });
// $nombres = [1, 3, 5, 7, 9]
```

---

**Q6. Qu'est-ce qu'un générateur (`yield`) et quand l'utiliser ?**

Les générateurs permettent d'itérer sur de grandes séquences sans charger tout en mémoire.

```php
function lireLigneFichier(string $path): \Generator {
    $handle = fopen($path, 'r');
    while (!feof($handle)) {
        yield fgets($handle);
    }
    fclose($handle);
}

foreach (lireLigneFichier('huge.csv') as $ligne) {
    traiter($ligne); // une ligne en mémoire à la fois
}
```

Utile pour : CSV volumineux, pagination de BDD, séquences infinies.

---

**Q7. Comment fonctionne la gestion des exceptions en PHP ? Quelle est la différence entre `Exception` et `Error` ?**

```php
try {
    // code risqué
} catch (InvalidArgumentException $e) {
    // exception spécifique
} catch (RuntimeException | LogicException $e) {
    // union de types (PHP 8)
} catch (Throwable $e) {
    // attrape Exception ET Error
} finally {
    // toujours exécuté
}
```

- `Exception` : erreurs applicatives (logique métier)
- `Error` : erreurs moteur PHP (`TypeError`, `ParseError`, `DivisionByZeroError`)
- `Throwable` : interface parente des deux depuis PHP 7

---

**Q8. Qu'est-ce que le PHP-FPM et comment interagit-il avec Nginx ?**

PHP-FPM (FastCGI Process Manager) est un gestionnaire de processus PHP. Nginx passe les requêtes `.php` à PHP-FPM via le protocole FastCGI sur un socket Unix ou TCP.

```
Client → Nginx (HTTP) → FastCGI → PHP-FPM → Votre code → Réponse
```

PHP-FPM gère un pool de workers, la mémoire maximale, les timeouts et les logs d'erreurs séparément de Nginx.

---

## 2 — Orienté Objet (POO)

**Q9. Expliquez les 4 piliers de la POO avec des exemples PHP.**

**Encapsulation** — cacher l'état interne :
```php
class CompteBancaire {
    private float $solde;

    public function deposer(float $montant): void {
        if ($montant <= 0) throw new \InvalidArgumentException();
        $this->solde += $montant;
    }

    public function getSolde(): float { return $this->solde; }
}
```

**Héritage** — spécialisation :
```php
class Animal { public function parler(): string { return '...'; } }
class Chien extends Animal { public function parler(): string { return 'Woof'; } }
```

**Polymorphisme** — même interface, comportements différents :
```php
function faireParler(Animal $animal): void {
    echo $animal->parler(); // Chien, Chat, etc.
}
```

**Abstraction** — contrat sans implémentation :
```php
abstract class Paiement {
    abstract public function payer(float $montant): bool;
    public function confirmer(): void { /* logique commune */ }
}
```

---

**Q10. Qu'est-ce que l'injection de dépendances ? Pourquoi est-ce important ?**

L'injection de dépendances (DI) consiste à fournir les dépendances d'un objet depuis l'extérieur plutôt qu'en les instanciant lui-même.

```php
// ❌ Couplage fort
class CommandeService {
    public function __construct() {
        $this->mailer = new MailerSMTP(); // couplé à SMTP
    }
}

// ✅ Injection de dépendance
class CommandeService {
    public function __construct(
        private readonly MailerInterface $mailer // découplé
    ) {}
}
```

Avantages : testabilité (mock), remplacement facile, respect du principe SOLID Open/Closed.

---

**Q11. Qu'est-ce que les principes SOLID ?**

| Principe | Description |
|---|---|
| **S**ingle Responsibility | Une classe = une seule raison de changer |
| **O**pen/Closed | Ouvert à l'extension, fermé à la modification |
| **L**iskov Substitution | Les sous-classes doivent être substituables |
| **I**nterface Segregation | Préférer des petites interfaces spécifiques |
| **D**ependency Inversion | Dépendre des abstractions, pas des implémentations |

---

**Q12. Différence entre classe abstraite et interface en PHP 8 ?**

```php
// Interface : contrat pur (méthodes publiques sans corps avant PHP 8)
interface PayableInterface {
    public function calculerTotal(): float;
    public function appliquerTaxe(float $taux): float;
}

// Classe abstraite : peut avoir état + implémentations partielles
abstract class DocumentBase {
    protected array $metadata = [];

    abstract public function render(): string; // à implémenter

    public function getMeta(string $key): mixed { // déjà implémenté
        return $this->metadata[$key] ?? null;
    }
}
```

Une classe peut implémenter plusieurs interfaces mais n'hériter que d'une seule classe abstraite.

---

## 3 — Laravel — Architecture & Cycle de vie

**Q13. Décrivez le cycle de vie d'une requête HTTP dans Laravel.**

```
index.php
  └─ Application::bootstrap()
       └─ Charge ServiceProviders (register → boot)
  └─ HTTP Kernel::handle()
       └─ Pipeline Middleware (global)
            └─ Router::dispatch()
                 └─ Résolution du contrôleur (IoC Container)
                      └─ Middleware de route
                           └─ Contrôleur → Action
                                └─ Response → Middleware (retour)
                                     └─ Envoi au client
```

---

**Q14. Qu'est-ce que le Service Container de Laravel ?**

C'est un conteneur IoC (Inversion of Control) qui gère les dépendances et résout automatiquement les injections de type dans les constructeurs.

```php
// Liaison simple
app()->bind(PaymentGatewayInterface::class, StripeGateway::class);

// Singleton (une seule instance)
app()->singleton(Cache::class, fn() => new RedisCache(config('redis')));

// Résolution automatique
class OrderController {
    public function __construct(
        private OrderService $service // injecté automatiquement
    ) {}
}
```

---

**Q15. Qu'est-ce qu'un Service Provider dans Laravel ? Donnez un exemple.**

Les Service Providers sont le point central de bootstrapping de Laravel. Chaque provider a deux méthodes : `register()` et `boot()`.

```php
class PaiementServiceProvider extends ServiceProvider {
    public function register(): void {
        $this->app->bind(
            PaiementInterface::class,
            fn($app) => new StripeService(config('services.stripe.key'))
        );
    }

    public function boot(): void {
        // S'exécute après tous les register()
        // Bon endroit pour les event listeners, routes, views
        $this->loadRoutesFrom(__DIR__.'/../routes/paiement.php');
    }
}
```

---

**Q16. Quelle est la différence entre `register()` et `boot()` dans un Service Provider ?**

- `register()` : ne lier que des services dans le container. Aucune dépendance sur d'autres providers car ils ne sont pas encore tous chargés.
- `boot()` : appelé après que tous les providers ont été enregistrés. Utiliser les services déjà liés, écouter des événements, publier des assets.

---

**Q17. Qu'est-ce qu'une Facade Laravel ? Comment fonctionne-t-elle ?**

Une Facade fournit une interface statique à des services dans le container, sans les instancier manuellement.

```php
// Utilisation façade
Cache::put('cle', 'valeur', 3600);

// Équivalent via container
app('cache')->put('cle', 'valeur', 3600);
```

La classe `Cache` étend `Illuminate\Support\Facades\Facade` et implémente `getFacadeAccessor()` qui retourne la clé du binding dans le container. PHP `__callStatic` délègue l'appel à l'instance résolue.

---

## 4 — Routing & Middleware

**Q18. Comment définir une ressource RESTful complète en Laravel ?**

```php
// routes/api.php
Route::apiResource('articles', ArticleController::class);

// Génère automatiquement :
// GET    /articles           → index
// POST   /articles           → store
// GET    /articles/{article} → show
// PUT    /articles/{article} → update
// DELETE /articles/{article} → destroy
```

---

**Q19. Comment créer un middleware personnalisé dans Laravel ?**

```bash
php artisan make:middleware VerifierAbonnement
```

```php
class VerifierAbonnement {
    public function handle(Request $request, Closure $next): Response {
        if (!$request->user()?->abonnementActif()) {
            return response()->json(['message' => 'Abonnement requis'], 403);
        }
        return $next($request);
    }
}
```

Enregistrement dans `bootstrap/app.php` (Laravel 11+) ou `Kernel.php` :
```php
Route::middleware('abonnement')->group(fn() =>
    Route::get('/premium', PremiumController::class)
);
```

---

**Q20. Différence entre middleware global, groupe et de route ?**

```php
// Global : s'applique à toutes les requêtes
->withMiddleware(function (Middleware $middleware) {
    $middleware->append(LogRequetes::class);
})

// Groupe : s'applique à un groupe de routes
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/dashboard', DashboardController::class);
});

// Route : uniquement sur une route
Route::get('/admin', AdminController::class)->middleware('can:admin');
```

---

**Q21. Qu'est-ce que le Route Model Binding ?**

Laravel résout automatiquement les modèles Eloquent à partir des paramètres de route.

```php
// Implicit binding
Route::get('/articles/{article}', function (Article $article) {
    return $article; // Article::findOrFail($id) automatique
});

// Custom binding avec colonne différente
Route::get('/articles/{article:slug}', ArticleController::class);

// Explicit binding dans RouteServiceProvider
Route::bind('article', fn($value) => Article::where('slug', $value)->firstOrFail());
```

---

## 5 — Eloquent ORM

**Q22. Expliquez les relations Eloquent et donnez des exemples.**

```php
// One-to-One
class User extends Model {
    public function profil(): HasOne {
        return $this->hasOne(Profil::class);
    }
}

// One-to-Many
class Article extends Model {
    public function commentaires(): HasMany {
        return $this->hasMany(Commentaire::class);
    }
}

// Many-to-Many
class Produit extends Model {
    public function categories(): BelongsToMany {
        return $this->belongsToMany(Categorie::class)
                    ->withPivot('ordre')
                    ->withTimestamps();
    }
}

// Polymorphique
class Image extends Model {
    public function imageable(): MorphTo {
        return $this->morphTo();
    }
}
class Article extends Model {
    public function images(): MorphMany {
        return $this->morphMany(Image::class, 'imageable');
    }
}
```

---

**Q23. Qu'est-ce que le problème N+1 en Eloquent ? Comment le résoudre ?**

Le problème N+1 survient quand une requête principale génère N requêtes supplémentaires.

```php
// ❌ N+1 : 1 requête pour articles + N requêtes pour chaque auteur
$articles = Article::all();
foreach ($articles as $article) {
    echo $article->auteur->nom; // requête par article !
}

// ✅ Eager loading : 2 requêtes seulement
$articles = Article::with('auteur')->get();

// ✅ Lazy eager loading (après récupération)
$articles = Article::all();
$articles->load('auteur');

// Détection avec Telescope ou :
Model::preventLazyLoading(!app()->isProduction());
```

---

**Q24. Différence entre `get()`, `first()`, `find()`, `firstOrFail()` et `findOrFail()` ?**

```php
Article::get()              // Collection de tous les articles
Article::first()            // Premier article ou null
Article::find(1)            // Article avec id=1 ou null
Article::firstOrFail()      // Premier article ou ModelNotFoundException
Article::findOrFail(1)      // Article id=1 ou ModelNotFoundException

Article::where('actif', true)->firstOrFail();   // Avec condition
```

---

**Q25. Qu'est-ce qu'un scope Eloquent ? Différence entre local et global scope ?**

```php
// Local scope : appelé manuellement
class Article extends Model {
    public function scopePublie(Builder $query): Builder {
        return $query->where('statut', 'publié');
    }

    public function scopeRecent(Builder $query, int $jours = 30): Builder {
        return $query->where('created_at', '>=', now()->subDays($jours));
    }
}

// Utilisation
Article::publié()->recent(7)->get();

// Global scope : appliqué automatiquement à toutes les requêtes
class ArticleScope implements Scope {
    public function apply(Builder $builder, Model $model): void {
        $builder->where('actif', true);
    }
}

class Article extends Model {
    protected static function booted(): void {
        static::addGlobalScope(new ArticleScope());
    }
}

// Ignorer un global scope
Article::withoutGlobalScope(ArticleScope::class)->get();
```

---

**Q26. Qu'est-ce que les Observers Eloquent ?**

Les Observers regroupent les écouteurs d'événements de modèle (`creating`, `created`, `updating`, `updated`, `deleting`, `deleted`, `restoring`, `restored`).

```php
class CommandeObserver {
    public function created(Commande $commande): void {
        Mail::to($commande->client)->send(new ConfirmationCommande($commande));
    }

    public function deleting(Commande $commande): void {
        $commande->lignes()->delete(); // nettoyage avant suppression
    }
}

// Enregistrement
Commande::observe(CommandeObserver::class);
```

---

**Q27. Comment fonctionne le soft delete en Eloquent ?**

```php
use Illuminate\Database\Eloquent\SoftDeletes;

class Article extends Model {
    use SoftDeletes;
    // Ajoute automatiquement WHERE deleted_at IS NULL à toutes les requêtes
}

// Migration
$table->softDeletes(); // colonne deleted_at

// Utilisation
$article->delete();           // soft delete (remplit deleted_at)
Article::withTrashed()->get(); // inclure supprimés
Article::onlyTrashed()->get(); // uniquement supprimés
$article->restore();           // restaurer
$article->forceDelete();       // suppression définitive
```

---

## 6 — MVC & Blade

**Q28. Comment fonctionne l'architecture MVC dans Laravel ?**

- **Modèle** : représente les données (`app/Models`), interagit avec la BDD via Eloquent
- **Vue** : présentation HTML (`resources/views`), templates Blade
- **Contrôleur** : orchestre la requête (`app/Http/Controllers`), appelle les services/modèles

```
Requête → Routeur → Contrôleur → Service → Modèle → BDD
                        ↓
                      Vue ← données
```

---

**Q29. Principales directives Blade à connaître ?**

```blade
{{-- Affichage échappé (XSS safe) --}}
{{ $variable }}

{{-- Affichage non échappé (attention) --}}
{!! $html !!}

{{-- Conditions --}}
@if($user->isAdmin())
    <span>Admin</span>
@elseif($user->isModo())
    <span>Modo</span>
@else
    <span>Membre</span>
@endif

{{-- Boucles --}}
@foreach($articles as $article)
    @if($loop->first) <ul> @endif
    <li>{{ $loop->index }}: {{ $article->titre }}</li>
    @if($loop->last) </ul> @endif
@endforeach

@forelse($articles as $article)
    <li>{{ $article->titre }}</li>
@empty
    <p>Aucun article.</p>
@endforelse

{{-- Layouts --}}
@extends('layouts.app')
@section('content')
    <h1>Contenu</h1>
@endsection

{{-- Composants --}}
@component('components.alerte', ['type' => 'success'])
    Opération réussie !
@endcomponent

{{-- Blade Components (Laravel 7+) --}}
<x-alerte type="success">Opération réussie !</x-alerte>

{{-- Auth --}}
@auth
    Connecté : {{ auth()->user()->name }}
@endauth

@can('modifier-article', $article)
    <a href="{{ route('articles.edit', $article) }}">Modifier</a>
@endcan
```

---

**Q30. Différence entre `@include`, `@extends`/`@section` et les composants Blade ?**

- `@include` : inclusion simple d'un sous-template (partials)
- `@extends`/`@section` : héritage de layout, le template enfant remplace des sections
- Composants Blade (`<x-composant>`) : encapsulation réutilisable avec props et slots typés, recommandée depuis Laravel 7

---

## 7 — API REST avec Laravel

**Q31. Comment créer une API REST versionnée dans Laravel ?**

```php
// routes/api.php
Route::prefix('v1')->name('v1.')->group(function () {
    Route::apiResource('articles', V1\ArticleController::class);
});

Route::prefix('v2')->name('v2.')->group(function () {
    Route::apiResource('articles', V2\ArticleController::class);
});
```

Structure des contrôleurs :
```
app/Http/Controllers/API/V1/ArticleController.php
app/Http/Controllers/API/V2/ArticleController.php
```

---

**Q32. Qu'est-ce qu'une API Resource dans Laravel ?**

Les API Resources transforment les modèles Eloquent en JSON structuré.

```php
// php artisan make:resource ArticleResource

class ArticleResource extends JsonResource {
    public function toArray(Request $request): array {
        return [
            'id'        => $this->id,
            'titre'     => $this->titre,
            'slug'      => $this->slug,
            'auteur'    => new UserResource($this->whenLoaded('auteur')),
            'tags'      => TagResource::collection($this->whenLoaded('tags')),
            'publie_le' => $this->published_at?->format('d/m/Y'),
            'liens'     => [
                'self' => route('api.v1.articles.show', $this->id),
            ],
        ];
    }
}

// Utilisation
return new ArticleResource($article);
return ArticleResource::collection(Article::with('auteur')->paginate());
```

---

**Q33. Comment gérer l'authentification API avec Laravel Sanctum ?**

```bash
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

```php
// Connexion → emission de token
public function login(LoginRequest $request): JsonResponse {
    if (!Auth::attempt($request->only('email', 'password'))) {
        throw new AuthenticationException();
    }
    $token = $request->user()->createToken(
        'api-token',
        ['articles:read', 'articles:write'] // abilities
    );
    return response()->json(['token' => $token->plainTextToken]);
}

// Protection des routes
Route::middleware('auth:sanctum')->group(function () {
    Route::apiResource('articles', ArticleController::class);
});

// Vérifier les abilities
$request->user()->tokenCan('articles:write');
```

---

**Q34. Comment implémenter la validation des requêtes API ?**

```php
// php artisan make:request StoreArticleRequest

class StoreArticleRequest extends FormRequest {
    public function authorize(): bool {
        return $this->user()->can('creer-article');
    }

    public function rules(): array {
        return [
            'titre'         => ['required', 'string', 'max:255'],
            'contenu'       => ['required', 'string', 'min:100'],
            'categorie_id'  => ['required', 'exists:categories,id'],
            'tags'          => ['array', 'max:5'],
            'tags.*'        => ['string', 'exists:tags,nom'],
            'publie_le'     => ['nullable', 'date', 'after:today'],
        ];
    }

    public function messages(): array {
        return [
            'titre.required' => 'Le titre est obligatoire.',
            'contenu.min'    => 'Le contenu doit contenir au moins :min caractères.',
        ];
    }
}
```

Laravel retourne automatiquement une erreur `422 Unprocessable Entity` avec les erreurs en JSON pour les requêtes API (détectées via `Accept: application/json`).

---

**Q35. Comment gérer les erreurs API de façon centralisée dans Laravel 11 ?**

```php
// bootstrap/app.php
->withExceptions(function (Exceptions $exceptions) {
    $exceptions->render(function (ModelNotFoundException $e, Request $request) {
        if ($request->expectsJson()) {
            return response()->json([
                'message' => 'Ressource introuvable.',
            ], 404);
        }
    });

    $exceptions->render(function (AuthorizationException $e, Request $request) {
        if ($request->expectsJson()) {
            return response()->json([
                'message' => 'Action non autorisée.',
            ], 403);
        }
    });
})
```

---

## 8 — Artisan CLI

**Q36. Quelles sont les commandes Artisan les plus importantes à connaître ?**

```bash
# Application
php artisan serve                        # Démarrer le serveur dev
php artisan key:generate                 # Générer APP_KEY
php artisan config:cache                 # Mettre en cache la config
php artisan route:cache                  # Mettre en cache les routes
php artisan view:cache                   # Compiler les vues Blade
php artisan optimize                     # Tout mettre en cache
php artisan optimize:clear               # Vider tous les caches

# Génération de code
php artisan make:model Article -mcrs    # Model + Migration + Controller + Resource + Seeder
php artisan make:controller ArticleController --api
php artisan make:request StoreArticleRequest
php artisan make:resource ArticleResource
php artisan make:middleware AuthToken
php artisan make:job EnvoyerEmail
php artisan make:event ArticlePublié
php artisan make:listener NotifierAbonnés --event=ArticlePublié
php artisan make:policy ArticlePolicy --model=Article

# Base de données
php artisan migrate
php artisan migrate:fresh --seed
php artisan migrate:rollback --step=3
php artisan db:seed --class=ArticleSeeder
php artisan tinker                       # REPL interactif

# Files d'attente
php artisan queue:work --queue=emails,default
php artisan queue:failed
php artisan queue:retry all
```

---

**Q37. Comment créer une commande Artisan personnalisée ?**

```php
// php artisan make:command EnvoyerRapportHebdo

class EnvoyerRapportHebdo extends Command {
    protected $signature = 'rapport:hebdo
                            {--destinataire=admin@example.com : Email du destinataire}
                            {--semaine= : Numéro de semaine (défaut: semaine courante)}';

    protected $description = 'Envoie le rapport hebdomadaire des ventes';

    public function handle(RapportService $rapportService): int {
        $email = $this->option('destinataire');
        $semaine = $this->option('semaine') ?? now()->weekOfYear;

        $this->info("Génération du rapport semaine {$semaine}...");

        try {
            $rapport = $rapportService->generer($semaine);
            Mail::to($email)->send(new RapportHebdo($rapport));
            $this->info("✅ Rapport envoyé à {$email}");
        } catch (\Exception $e) {
            $this->error("❌ Erreur : {$e->getMessage()}");
            return Command::FAILURE;
        }

        return Command::SUCCESS;
    }
}
```

Planification dans `routes/console.php` :
```php
Schedule::command('rapport:hebdo')->weeklyOn(1, '08:00'); // Lundi 8h
```

---

## 9 — PHPUnit & Tests

**Q38. Quelle est la différence entre test unitaire, test d'intégration et test fonctionnel dans Laravel ?**

| Type | Ce qu'il teste | Outils |
|---|---|---|
| Unitaire | Une classe/méthode isolée | PHPUnit, Mockery |
| Intégration | Interaction entre composants (BDD, services) | PHPUnit + SQLite in-memory |
| Fonctionnel (Feature) | Une fonctionnalité complète HTTP | `$this->get()`, `actingAs()` |

---

**Q39. Comment écrire un test de feature HTTP dans Laravel ?**

```php
class ArticleControllerTest extends TestCase {
    use RefreshDatabase;

    public function test_un_utilisateur_peut_lister_les_articles(): void {
        $user = User::factory()->create();
        Article::factory()->count(3)->create(['auteur_id' => $user->id]);

        $response = $this->actingAs($user, 'sanctum')
                         ->getJson('/api/v1/articles');

        $response->assertOk()
                 ->assertJsonCount(3, 'data')
                 ->assertJsonStructure([
                     'data' => [['id', 'titre', 'auteur']],
                     'meta' => ['total', 'per_page'],
                 ]);
    }

    public function test_un_invité_ne_peut_pas_créer_un_article(): void {
        $this->postJson('/api/v1/articles', ['titre' => 'Test'])
             ->assertUnauthorized();
    }
}
```

---

**Q40. Comment mocker des dépendances dans les tests Laravel ?**

```php
// Mocker une Facade
Mail::fake();
Storage::fake('s3');
Queue::fake();
Event::fake();
Notification::fake();

// Après l'action
Mail::assertSent(ConfirmationCommande::class, function ($mail) use ($commande) {
    return $mail->hasTo($commande->client->email);
});
Queue::assertPushed(EnvoyerEmail::class);

// Mocker un service via le container
$this->mock(PaiementService::class, function (MockInterface $mock) {
    $mock->shouldReceive('payer')
         ->with(99.99)
         ->once()
         ->andReturn(true);
});
```

---

**Q41. Qu'est-ce qu'un Factory en Laravel et comment le configurer ?**

```php
// database/factories/ArticleFactory.php
class ArticleFactory extends Factory {
    public function definition(): array {
        return [
            'titre'      => fake()->sentence(),
            'slug'       => fake()->unique()->slug(),
            'contenu'    => fake()->paragraphs(3, true),
            'statut'     => fake()->randomElement(['brouillon', 'publié']),
            'auteur_id'  => User::factory(),
            'publié_le'  => fake()->optional()->dateTimeThisYear(),
        ];
    }

    // States personnalisés
    public function publié(): static {
        return $this->state(['statut' => 'publié', 'publié_le' => now()]);
    }

    public function brouillon(): static {
        return $this->state(['statut' => 'brouillon', 'publié_le' => null]);
    }
}

// Utilisation dans les tests
Article::factory()->count(5)->publié()->create();
Article::factory()->for($user, 'auteur')->create(['titre' => 'Mon article']);
```

---

## 10 — MySQL & Base de Données

**Q42. Comment optimiser une requête SQL lente dans Laravel ?**

```php
// 1. Identifier : activer le query log
DB::enableQueryLog();
// ... exécuter les requêtes
dd(DB::getQueryLog());

// 2. Analyser avec EXPLAIN
DB::select('EXPLAIN SELECT * FROM articles WHERE statut = ?', ['publié']);

// 3. Ajouter un index dans une migration
Schema::table('articles', function (Blueprint $table) {
    $table->index(['statut', 'published_at']); // index composite
    $table->fullText('contenu');               // recherche full-text
});

// 4. Utiliser select() pour limiter les colonnes
Article::select('id', 'titre', 'auteur_id')->with('auteur:id,nom')->get();

// 5. Chunk pour les gros volumes
Article::chunk(500, function ($articles) {
    foreach ($articles as $article) {
        ProcessArticle::dispatch($article);
    }
});
```

---

**Q43. Expliquez les transactions de base de données dans Laravel.**

```php
// Méthode 1 : callback automatique (recommandée)
DB::transaction(function () use ($commande, $items) {
    $commande = Commande::create($commande);
    foreach ($items as $item) {
        $commande->lignes()->create($item);
        Stock::decrementer($item['produit_id'], $item['quantite']);
    }
});
// Rollback automatique si une exception est levée

// Méthode 2 : manuelle
DB::beginTransaction();
try {
    // opérations...
    DB::commit();
} catch (\Throwable $e) {
    DB::rollBack();
    throw $e;
}

// Tentatives en cas de deadlock
DB::transaction(function () { /* ... */ }, attempts: 3);
```

---

**Q44. Différence entre `whereHas` et `with` en Eloquent ?**

```php
// with() : eager load, charge les relations
// Ne filtre PAS les articles, charge juste les commentaires liés
$articles = Article::with('commentaires')->get();
// Retourne TOUS les articles, avec leurs commentaires chargés

// whereHas() : filtre les modèles selon une relation
// Retourne UNIQUEMENT les articles qui ont des commentaires approuvés
$articles = Article::whereHas('commentaires', function ($q) {
    $q->where('approuvé', true);
})->get();

// Combinaison courante : filtrer ET charger
$articles = Article::whereHas('commentaires')
                   ->with(['commentaires' => fn($q) => $q->where('approuvé', true)])
                   ->get();
```

---

**Q45. Qu'est-ce que l'indexation MySQL et quand créer des index ?**

Créer un index quand :
- Colonne utilisée dans `WHERE`, `ORDER BY`, `GROUP BY`
- Colonnes de jointures (`JOIN ON`)
- Colonnes de clé étrangère

```sql
-- Index simple
CREATE INDEX idx_statut ON articles(statut);

-- Index composite (ordre important : du plus sélectif au moins)
CREATE INDEX idx_statut_date ON articles(statut, published_at);

-- Index unique
CREATE UNIQUE INDEX idx_slug ON articles(slug);

-- Full-text (pour MATCH AGAINST)
CREATE FULLTEXT INDEX ft_contenu ON articles(titre, contenu);
```

Éviter d'indexer : colonnes à faible cardinalité (booléens), tables très petites, colonnes rarement utilisées en filtre.

---

## 11 — Sécurité

**Q46. Comment Laravel protège-t-il contre les injections SQL ?**

Laravel utilise PDO avec des requêtes préparées (paramètres liés) par défaut. Eloquent et le Query Builder préparent automatiquement les requêtes.

```php
// ✅ Sécurisé : paramètre lié
User::where('email', $request->email)->first();
DB::select('SELECT * FROM users WHERE email = ?', [$email]);

// ❌ Dangereux : interpolation directe
DB::select("SELECT * FROM users WHERE email = '{$email}'"); // JAMAIS

// Whitelist pour les colonnes dynamiques
$colonne = in_array($request->sort, ['nom', 'email', 'created_at'])
    ? $request->sort
    : 'created_at';
User::orderBy($colonne)->get();
```

---

**Q47. Comment fonctionne la protection CSRF dans Laravel ?**

Laravel génère un token CSRF unique par session. Pour les formulaires Blade, la directive `@csrf` insère un champ caché. Le middleware `VerifyCsrfToken` valide le token à chaque requête `POST/PUT/PATCH/DELETE`.

```blade
<form method="POST" action="/articles">
    @csrf
    {{-- Génère : <input type="hidden" name="_token" value="..."> --}}
</form>
```

Pour les requêtes AJAX :
```javascript
axios.defaults.headers.common['X-CSRF-TOKEN'] =
    document.querySelector('meta[name="csrf-token"]').getAttribute('content');
```

Les routes API (`routes/api.php`) sont exemptées par défaut (utilisent des tokens Sanctum à la place).

---

**Q48. Quelles bonnes pratiques pour stocker les mots de passe dans Laravel ?**

```php
// Hash automatique avec bcrypt (défaut) ou argon2
$user->password = Hash::make($request->password);

// Vérification
if (Hash::check($request->password, $user->password)) { /* ok */ }

// Rehash si le coût change (Laravel le fait automatiquement via Auth)
if (Hash::needsRehash($user->password)) {
    $user->password = Hash::make($plaintext);
    $user->save();
}
```

Ne jamais : stocker en clair, utiliser MD5/SHA1, encoder en base64.

---

**Q49. Comment implémenter l'autorisation avec les Policies Laravel ?**

```php
// php artisan make:policy ArticlePolicy --model=Article

class ArticlePolicy {
    public function update(User $user, Article $article): bool {
        return $user->id === $article->auteur_id || $user->isAdmin();
    }

    public function delete(User $user, Article $article): bool {
        return $user->id === $article->auteur_id || $user->can('supprimer-tout');
    }
}

// Utilisation dans un contrôleur
public function update(Request $request, Article $article): Response {
    $this->authorize('update', $article); // Lance AuthorizationException si refus
    // ...
}

// Dans Blade
@can('update', $article)
    <a href="{{ route('articles.edit', $article) }}">Modifier</a>
@endcan

// Via Gate
Gate::allows('update', $article);
```

---

## 12 — Performance & Optimisation

**Q50. Comment utiliser le cache dans Laravel ?**

```php
// Mettre en cache avec TTL
Cache::put('articles:une', $article, now()->addHour());

// Récupérer ou calculer (remember)
$articles = Cache::remember('articles:accueil', 3600, function () {
    return Article::publié()->with('auteur')->latest()->take(10)->get();
});

// Cache permanent
Cache::forever('config:generale', $config);

// Supprimer
Cache::forget('articles:accueil');
Cache::tags(['articles'])->flush(); // Redis/Memcached uniquement

// Drivers disponibles
// file (défaut), database, redis, memcached, array (tests), dynamodb
```

---

**Q51. Qu'est-ce que les files d'attente (Queues) dans Laravel ? Quand les utiliser ?**

```php
// Créer un Job
// php artisan make:job EnvoyerEmailBienvenue

class EnvoyerEmailBienvenue implements ShouldQueue {
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public int $tries = 3;        // tentatives max
    public int $timeout = 60;     // secondes
    public int $backoff = 30;     // délai entre tentatives

    public function __construct(private readonly User $user) {}

    public function handle(MailerInterface $mailer): void {
        $mailer->to($this->user)->send(new EmailBienvenue($this->user));
    }

    public function failed(\Throwable $e): void {
        Log::error("Échec envoi email {$this->user->id}: {$e->getMessage()}");
    }
}

// Dispatcher
EnvoyerEmailBienvenue::dispatch($user);
EnvoyerEmailBienvenue::dispatch($user)->onQueue('emails')->delay(now()->addMinutes(5));
```

Cas d'usage : envoi d'emails, traitement d'images, notifications, webhooks, exports CSV.

---

**Q52. Comment optimiser les performances d'une application Laravel en production ?**

```bash
# Compilation et cache
php artisan optimize          # config + routes + events + views
php artisan icons:cache       # si Blade Icons
composer install --no-dev --optimize-autoloader

# Configuration
APP_ENV=production
APP_DEBUG=false
CACHE_DRIVER=redis
QUEUE_CONNECTION=redis
SESSION_DRIVER=redis
LOG_LEVEL=warning
```

Côté code :
- Utiliser `->select()` pour limiter les colonnes
- Activer le cache de requêtes fréquentes
- Lazy collections pour les gros volumes
- HTTP/2 + gzip côté Nginx
- CDN pour les assets
- `php artisan queue:work --daemon` pour les workers persistants

---

## 13 — Bonnes Pratiques & Patterns

**Q53. Qu'est-ce qu'un Repository Pattern dans Laravel et faut-il l'utiliser ?**

Le Repository Pattern abstrait la couche d'accès aux données.

```php
interface ArticleRepositoryInterface {
    public function findAll(): Collection;
    public function findById(int $id): ?Article;
    public function store(array $data): Article;
}

class EloquentArticleRepository implements ArticleRepositoryInterface {
    public function findAll(): Collection {
        return Article::with('auteur')->latest()->get();
    }
    // ...
}

// Binding dans un ServiceProvider
$this->app->bind(ArticleRepositoryInterface::class, EloquentArticleRepository::class);
```

**Faut-il l'utiliser ?** Dans la plupart des projets Laravel, le Pattern Repository ajoute de la complexité sans bénéfice réel car Eloquent est déjà une abstraction. Il est justifié si : vous devez pouvoir switcher de source de données, vous avez une logique de requête très complexe et réutilisée, ou votre équipe est grande et nécessite des interfaces strictes.

---

**Q54. Expliquez le pattern Service Layer (Service Classes) dans Laravel.**

```php
class ArticleService {
    public function __construct(
        private readonly ArticleRepository $repository,
        private readonly ImageService $imageService,
        private readonly TagService $tagService,
    ) {}

    public function publier(array $données, User $auteur): Article {
        return DB::transaction(function () use ($données, $auteur) {
            $article = $this->repository->create([
                ...$données,
                'auteur_id'   => $auteur->id,
                'statut'      => 'publié',
                'publié_le'   => now(),
            ]);

            if (isset($données['image'])) {
                $this->imageService->attacherÀArticle($données['image'], $article);
            }

            $this->tagService->synchroniser($article, $données['tags'] ?? []);

            event(new ArticlePublié($article));

            return $article;
        });
    }
}

// Contrôleur fin (thin controller)
class ArticleController extends Controller {
    public function store(StoreArticleRequest $request, ArticleService $service): JsonResponse {
        $article = $service->publier($request->validated(), $request->user());
        return new ArticleResource($article);
    }
}
```

---

**Q55. Qu'est-ce qu'un Action Class et en quoi diffère-t-il d'un Service ?**

Un **Action** représente une seule opération métier (Single Responsibility maximale), tandis qu'un Service peut regrouper plusieurs opérations liées.

```php
// Action : une responsabilité unique
class PublierArticleAction {
    public function __invoke(Article $article, User $auteur): Article {
        $article->update(['statut' => 'publié', 'publié_le' => now()]);
        event(new ArticlePublié($article));
        return $article;
    }
}

// Contrôleur
public function publish(Article $article, PublierArticleAction $action): JsonResponse {
    $this->authorize('publish', $article);
    return new ArticleResource($action($article, request()->user()));
}
```

---

**Q56. Quelles sont les bonnes pratiques pour les migrations Laravel ?**

```php
// ✅ Une migration = une opération atomique
// ✅ Toujours implémenter down() pour le rollback
// ✅ Ne jamais modifier une migration déjà en production
// ✅ Utiliser des noms descriptifs

class CreateArticlesTable extends Migration {
    public function up(): void {
        Schema::create('articles', function (Blueprint $table) {
            $table->id();
            $table->foreignId('auteur_id')->constrained('users')->cascadeOnDelete();
            $table->string('titre');
            $table->string('slug')->unique();
            $table->longText('contenu');
            $table->enum('statut', ['brouillon', 'publié', 'archivé'])->default('brouillon');
            $table->timestamp('publié_le')->nullable();
            $table->timestamps();
            $table->softDeletes();

            $table->index(['statut', 'publié_le']);
        });
    }

    public function down(): void {
        Schema::dropIfExists('articles');
    }
}
```

---

**Q57. Comment gérer les variables d'environnement et la configuration dans Laravel ?**

```php
// .env (jamais commité dans Git)
DB_HOST=127.0.0.1
STRIPE_KEY=sk_live_...

// config/services.php (couche d'abstraction)
'stripe' => [
    'key'    => env('STRIPE_KEY'),
    'secret' => env('STRIPE_SECRET'),
],

// Dans le code : config() pas env()
// ✅
$clé = config('services.stripe.key');

// ❌ Éviter en dehors des fichiers config/
$clé = env('STRIPE_KEY'); // ne fonctionne pas après config:cache
```

Utiliser des fichiers `.env.example` (commité), `.env.testing` pour les tests, et des secrets chiffrés pour la production.

---

## 🔗 Ressources Complémentaires

- [Documentation officielle Laravel](https://laravel.com/docs)
- [PHP The Right Way](https://phptherightway.com)
- [Laracasts](https://laracasts.com)
- [Laravel News](https://laravel-news.com)
- [Spatie — Bonnes pratiques Laravel](https://spatie.be/guidelines/laravel-php)

---

## 📄 Licence

Ce document est libre d'utilisation à des fins éducatives et de préparation aux entretiens.

---

*Maintenu avec ❤️ pour la communauté PHP/Laravel francophone*
