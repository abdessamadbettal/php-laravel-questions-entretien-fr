# 🐘 PHP & Laravel — Questions d'Entretien Complètes (Français)

> Collection exhaustive de questions d'entretien technique en français couvrant PHP, Laravel, Eloquent ORM, MVC, API REST, Artisan CLI, PHPUnit, MySQL, MongoDB, Redis, Inertia.js, et les bonnes pratiques avancées de développement.

---

## 📋 Table des Matières

- [🐘 PHP \& Laravel — Questions d'Entretien Complètes (Français)](#-php--laravel--questions-dentretien-complètes-français)
  - [📋 Table des Matières](#-table-des-matières)
  - [1 — PHP Fondamentaux](#1--php-fondamentaux)
    - [Q3. Différence entre `include`, `require`, `include_once` et `require_once`](#q3-différence-entre-include-require-include_once-et-require_once)
  - [2 — Orienté Objet (POO)](#2--orienté-objet-poo)
  - [3 — Laravel — Architecture \& Cycle de vie](#3--laravel--architecture--cycle-de-vie)
  - [4 — Routing \& Middleware](#4--routing--middleware)
  - [5 — Eloquent ORM](#5--eloquent-orm)
  - [6 — MVC \& Blade](#6--mvc--blade)
  - [7 — API REST avec Laravel](#7--api-rest-avec-laravel)
  - [8 — Artisan CLI](#8--artisan-cli)
  - [9 — PHPUnit \& Tests](#9--phpunit--tests)
  - [10 — MySQL \& Base de Données](#10--mysql--base-de-données)
  - [11 — MongoDB avec Laravel](#11--mongodb-avec-laravel)
  - [12 — Redis avec Laravel](#12--redis-avec-laravel)
  - [13 — Inertia.js avec Laravel](#13--inertiajs-avec-laravel)
  - [14 — Sécurité](#14--sécurité)
  - [15 — Performance \& Optimisation](#15--performance--optimisation)
  - [16 — Bonnes Pratiques \& Patterns](#16--bonnes-pratiques--patterns)
  - [17 — Laravel Avancé](#17--laravel-avancé)
  - [18 — Architecture \& Scalabilité](#18--architecture--scalabilité)
  - [19 — DevOps \& Déploiement](#19--devops--déploiement)
  - [20 — Questions Situationnelles \& Conception](#20--questions-situationnelles--conception)
  - [🔗 Ressources Complémentaires](#-ressources-complémentaires)
  - [📊 Résumé des Topics par Niveau](#-résumé-des-topics-par-niveau)
  - [📄 Licence](#-licence)

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

Le type juggling est la conversion automatique de types par PHP lors de comparaisons non strictes.

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

### Q3. Différence entre `include`, `require`, `include_once` et `require_once`

Ces quatre instructions PHP servent à insérer le contenu d'un fichier dans un autre. La différence repose sur deux critères : **la gestion des erreurs** (si le fichier est introuvable) et **la vérification des doublons**.

| Instruction | Si le fichier est introuvable | Inclusion multiple ? |
| :--- | :--- | :--- |
| **`include`** | ⚠️ **Warning** (le script continue) | Oui |
| **`require`** | 🛑 **Fatal Error** (le script s'arrête) | Oui |
| **`include_once`** | ⚠️ **Warning** (le script continue) | Non (ignoré si déjà inclus) |
| **`require_once`** | 🛑 **Fatal Error** (le script s'arrête) | Non (ignoré si déjà inclus) |

**💡 Bonnes pratiques d'utilisation :**
* Privilégiez **`require_once`** pour le chargement de classes, de dépendances ou de fichiers de configuration critiques. Cela évite de faire planter le code avec des erreurs de type "Cannot redeclare class".
* Utilisez **`include`** pour des éléments visuels ou optionnels (comme un composant HTML de pied de page). Si le fichier manque, l'utilisateur verra quand même le reste de la page.

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

**Q9. Quelles sont les nouveautés majeures de PHP 8.x ?**

```php
// PHP 8.0 — Match expression
$résultat = match($statut) {
    'actif', 'vérifié' => 'Utilisateur actif',
    'banni'            => 'Accès refusé',
    default            => 'Statut inconnu',
};

// PHP 8.0 — Named arguments
htmlspecialchars(string: $valeur, double_encode: false);

// PHP 8.0 — Nullsafe operator
$ville = $user?->getAdresse()?->getVille()?->getNom();

// PHP 8.0 — Union types
function traiter(int|string $id): void {}

// PHP 8.1 — Enums
enum Statut: string {
    case Actif   = 'actif';
    case Inactif = 'inactif';
    case Banni   = 'banni';
}
$s = Statut::Actif;
echo $s->value; // 'actif'

// PHP 8.1 — Fibers (coroutines légères)
$fiber = new Fiber(function(): void {
    $valeur = Fiber::suspend('premier');
    echo "Valeur reçue : {$valeur}";
});
$résultat = $fiber->start(); // 'premier'
$fiber->resume('deuxième');

// PHP 8.1 — Readonly properties
class Point {
    public function __construct(
        public readonly float $x,
        public readonly float $y,
    ) {}
}

// PHP 8.2 — Readonly classes
readonly class DTOArticle {
    public function __construct(
        public string $titre,
        public string $contenu,
    ) {}
}

// PHP 8.3 — Typed class constants
class Config {
    const string VERSION = '1.0.0';
    const int MAX_RETRY  = 3;
}
```

---

**Q10. Qu'est-ce que l'autoloading PSR-4 et comment Composer le gère-t-il ?**

PSR-4 est un standard d'autoloading qui mappe les namespaces à des répertoires.

```json
// composer.json
{
    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\": "database/"
        }
    }
}
```

```php
// app/Services/PaiementService.php → namespace App\Services
namespace App\Services;

class PaiementService {}

// Utilisation (résolu automatiquement)
use App\Services\PaiementService;
$service = new PaiementService();
```

Après modification : `composer dump-autoload --optimize`.

---

**Q11. Différence entre `array_merge` et l'opérateur `+` pour les tableaux ?**

```php
$a = ['a' => 1, 'b' => 2];
$b = ['b' => 3, 'c' => 4];

array_merge($a, $b);
// ['a' => 1, 'b' => 3, 'c' => 4] — les clés string de $b écrasent $a

$a + $b;
// ['a' => 1, 'b' => 2, 'c' => 4] — les clés de $a sont préservées

// Pour les tableaux indexés :
array_merge([1, 2], [3, 4]); // [1, 2, 3, 4] — réindexe
[1, 2] + [3, 4];             // [1, 2]        — clés 0,1 déjà présentes dans $a
```

---

**Q12. Comment fonctionne la gestion mémoire et le garbage collector en PHP ?**

PHP utilise un compteur de références (reference counting). Quand le compteur tombe à 0, la mémoire est libérée immédiatement. Un GC cyclique gère les références circulaires.

```php
// Libérer manuellement
unset($grandeVariable);
gc_collect_cycles(); // force le GC cyclique

// Surveiller la mémoire
echo memory_get_usage(true);     // mémoire utilisée
echo memory_get_peak_usage(true); // pic d'utilisation

// Limite (php.ini)
ini_set('memory_limit', '256M');
```

---

**Q13. Qu'est-ce que la programmation fonctionnelle en PHP ? `array_reduce` et les closures.**

```php
// Closure avec use
$taxe = 0.20;
$appliquerTaxe = function(float $prix) use ($taxe): float {
    return $prix * (1 + $taxe);
};

// Arrow function (PHP 7.4+) — capture automatique
$multiplier = fn(float $prix) => $prix * (1 + $taxe);

// array_reduce : agrégation
$total = array_reduce($panier, function(float $carry, array $item): float {
    return $carry + ($item['prix'] * $item['quantite']);
}, 0.0);

// Pipeline fonctionnel
$résultat = array_filter(
    array_map(
        fn($p) => $p * 1.2,
        $prix
    ),
    fn($p) => $p > 10
);
```

---

**Q14. Qu'est-ce que SPL (Standard PHP Library) ? Donnez des exemples utiles.**

```php
// SplStack
$pile = new SplStack();
$pile->push('premier');
$pile->push('deuxième');
echo $pile->pop(); // 'deuxième'

// SplQueue
$file = new SplQueue();
$file->enqueue('tâche1');
$file->dequeue();

// SplDoublyLinkedList, SplMinHeap, SplMaxHeap
$tas = new SplMinHeap();
$tas->insert(5);
$tas->insert(1);
$tas->insert(3);
echo $tas->extract(); // 1

// ArrayObject (tableau en objet)
$obj = new ArrayObject(['a' => 1, 'b' => 2]);
$obj['c'] = 3;
echo count($obj); // 3
```

---

## 2 — Orienté Objet (POO)

**Q15. Expliquez les 4 piliers de la POO avec des exemples PHP.**

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
    echo $animal->parler();
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

**Q16. Qu'est-ce que l'injection de dépendances ?**

L'injection de dépendances (DI) consiste à fournir les dépendances d'un objet depuis l'extérieur.

```php
// ❌ Couplage fort
class CommandeService {
    public function __construct() {
        $this->mailer = new MailerSMTP();
    }
}

// ✅ Injection de dépendance
class CommandeService {
    public function __construct(
        private readonly MailerInterface $mailer
    ) {}
}
```

---

**Q17. Qu'est-ce que les principes SOLID ?**

| Principe | Description |
|---|---|
| **S**ingle Responsibility | Une classe = une seule raison de changer |
| **O**pen/Closed | Ouvert à l'extension, fermé à la modification |
| **L**iskov Substitution | Les sous-classes doivent être substituables |
| **I**nterface Segregation | Préférer des petites interfaces spécifiques |
| **D**ependency Inversion | Dépendre des abstractions, pas des implémentations |

---

**Q18. Différence entre classe abstraite et interface en PHP 8 ?**

```php
// Interface : contrat pur
interface PayableInterface {
    public function calculerTotal(): float;
    public function appliquerTaxe(float $taux): float;
}

// Classe abstraite : peut avoir état + implémentations partielles
abstract class DocumentBase {
    protected array $metadata = [];
    abstract public function render(): string;

    public function getMeta(string $key): mixed {
        return $this->metadata[$key] ?? null;
    }
}
```

Une classe peut implémenter plusieurs interfaces mais n'hériter que d'une seule classe abstraite.

---

**Q19. Qu'est-ce que le pattern Decorator en PHP ?**

Le Decorator ajoute des comportements à un objet dynamiquement sans modifier sa classe.

```php
interface Logger {
    public function log(string $message): void;
}

class FileLogger implements Logger {
    public function log(string $message): void {
        file_put_contents('app.log', $message . PHP_EOL, FILE_APPEND);
    }
}

class TimestampLogger implements Logger {
    public function __construct(private Logger $inner) {}

    public function log(string $message): void {
        $this->inner->log('[' . date('Y-m-d H:i:s') . '] ' . $message);
    }
}

class PrefixLogger implements Logger {
    public function __construct(private Logger $inner, private string $prefix) {}

    public function log(string $message): void {
        $this->inner->log("[{$this->prefix}] {$message}");
    }
}

// Usage
$logger = new PrefixLogger(new TimestampLogger(new FileLogger()), 'APP');
$logger->log('Démarrage'); // [APP] [2024-01-01 12:00:00] Démarrage
```

---

**Q20. Qu'est-ce que le design pattern Observer (Event/Listener) ?**

```php
interface Observateur {
    public function notifier(string $evenement, mixed $données): void;
}

interface Sujet {
    public function abonner(string $evenement, Observateur $obs): void;
    public function publier(string $evenement, mixed $données): void;
}

class GestionnaireEvenements implements Sujet {
    private array $listeners = [];

    public function abonner(string $evenement, Observateur $obs): void {
        $this->listeners[$evenement][] = $obs;
    }

    public function publier(string $evenement, mixed $données): void {
        foreach ($this->listeners[$evenement] ?? [] as $obs) {
            $obs->notifier($evenement, $données);
        }
    }
}
```

---

**Q21. Qu'est-ce que la covariance et la contravariance en PHP 7.4+ ?**

```php
class Animal {}
class Chat extends Animal {}

interface Usine {
    public function créer(): Animal;
}

// Covariance : le type de retour peut être plus spécifique
class UsineChats implements Usine {
    public function créer(): Chat { // ✅ Chat est plus spécifique qu'Animal
        return new Chat();
    }
}

interface Nourrisseur {
    public function nourrir(Chat $animal): void;
}

// Contravariance : le type du paramètre peut être moins spécifique
class NourrisseurUniversel implements Nourrisseur {
    public function nourrir(Animal $animal): void { // ✅ accepte plus large
        // nourrit n'importe quel animal
    }
}
```

---

**Q22. Qu'est-ce qu'un Value Object ? Différence avec une Entity ?**

```php
// Value Object : immuable, identité par valeur, pas d'ID
final class Argent {
    public function __construct(
        public readonly int $montant,    // en centimes
        public readonly string $devise,
    ) {}

    public function ajouter(Argent $autre): self {
        if ($this->devise !== $autre->devise) {
            throw new \DomainException('Devises incompatibles');
        }
        return new self($this->montant + $autre->montant, $this->devise);
    }

    public function equals(Argent $autre): bool {
        return $this->montant === $autre->montant
            && $this->devise === $autre->devise;
    }
}

// Entity : identité par ID, mutable
class Commande {
    private CommandeId $id;     // identité propre
    private Argent $total;      // contient un Value Object
    private Statut $statut;
}
```

---

## 3 — Laravel — Architecture & Cycle de vie

**Q23. Décrivez le cycle de vie d'une requête HTTP dans Laravel.**

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

**Q24. Qu'est-ce que le Service Container de Laravel ?**

C'est un conteneur IoC (Inversion of Control) qui gère les dépendances.

```php
// Liaison simple
app()->bind(PaymentGatewayInterface::class, StripeGateway::class);

// Singleton
app()->singleton(Cache::class, fn() => new RedisCache(config('redis')));

// Instance
app()->instance('config', $configObject);

// Résolution contextuelle
$this->app->when(ArticleController::class)
    ->needs(RepositoryInterface::class)
    ->give(EloquentArticleRepository::class);

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

**Q26. Différence entre `register()` et `boot()` dans un Service Provider ?**

- `register()` : ne lier que des services dans le container. Aucune dépendance sur d'autres providers.
- `boot()` : appelé après que tous les providers ont été enregistrés. Utiliser les services, écouter des événements, publier des assets.

---

**Q27. Qu'est-ce qu'une Facade Laravel ?**

```php
// Utilisation façade
Cache::put('cle', 'valeur', 3600);

// Équivalent via container
app('cache')->put('cle', 'valeur', 3600);

// Créer une facade personnalisée
class MaFacade extends Facade {
    protected static function getFacadeAccessor(): string {
        return 'mon-service'; // clé de binding dans le container
    }
}
```

PHP `__callStatic` délègue l'appel à l'instance résolue.

---

**Q28. Qu'est-ce que le pipeline Laravel et comment l'utiliser ?**

```php
use Illuminate\Pipeline\Pipeline;

$résultat = app(Pipeline::class)
    ->send($commande)
    ->through([
        ValiderStock::class,
        AppliquerReduction::class,
        CalculerTaxes::class,
        EnregistrerCommande::class,
    ])
    ->thenReturn();

// Chaque étape
class ValiderStock {
    public function handle(Commande $commande, Closure $next): Commande {
        if (!$this->stockDisponible($commande)) {
            throw new StockInsuffisantException();
        }
        return $next($commande);
    }
}
```

---

**Q29. Qu'est-ce que Macroable dans Laravel ?**

```php
// Étendre les classes Laravel sans les modifier
use Illuminate\Support\Str;

Str::macro('camelToSnake', function(string $value): string {
    return strtolower(preg_replace('/[A-Z]/', '_$0', lcfirst($value)));
});

echo Str::camelToSnake('monNomDeVariable'); // mon_nom_de_variable

// Fonctionne aussi sur Collection, Response, etc.
Collection::macro('toAssoc', function() {
    return $this->mapWithKeys(fn($item) => [$item['key'] => $item['value']]);
});
```

---

## 4 — Routing & Middleware

**Q30. Comment définir une ressource RESTful complète en Laravel ?**

```php
Route::apiResource('articles', ArticleController::class);

// Génère :
// GET    /articles           → index
// POST   /articles           → store
// GET    /articles/{article} → show
// PUT    /articles/{article} → update
// DELETE /articles/{article} → destroy

// Ressources imbriquées
Route::apiResource('articles.commentaires', CommentaireController::class)
     ->shallow(); // évite /articles/{article}/commentaires/{commentaire}
```

---

**Q31. Comment créer un middleware personnalisé dans Laravel ?**

```bash
php artisan make:middleware VerifierAbonnement
```

```php
class VerifierAbonnement {
    public function handle(Request $request, Closure $next, string $plan = 'pro'): Response {
        if (!$request->user()?->aLePlan($plan)) {
            return response()->json(['message' => "Plan {$plan} requis"], 403);
        }
        return $next($request);
    }
}

// Avec paramètre
Route::middleware('abonnement:enterprise')->group(fn() =>
    Route::get('/analytics', AnalyticsController::class)
);
```

---

**Q32. Qu'est-ce que le Rate Limiting dans Laravel ?**

```php
// bootstrap/app.php (Laravel 11+)
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
});

RateLimiter::for('connexion', function (Request $request) {
    return [
        Limit::perMinute(5)->by($request->ip()),
        Limit::perDay(20)->by($request->input('email')),
    ];
});

// Application sur une route
Route::middleware('throttle:api')->group(function () {
    Route::get('/articles', ArticleController::class);
});
```

---

**Q33. Qu'est-ce que le Route Model Binding ?**
Laravel résout automatiquement les modèles Eloquent à partir des paramètres de route.

```php
// Implicit binding
Route::get('/articles/{article}', function (Article $article) {
    return $article; // Article::findOrFail($id) automatique
});

// Custom binding avec colonne différente
Route::get('/articles/{article:slug}', ArticleController::class);

// Explicit binding
Route::bind('article', fn($value) => Article::where('slug', $value)->firstOrFail());

// Scoped bindings (Laravel 9+)
Route::get('/users/{user}/articles/{article}', function (User $user, Article $article) {
    // article appartient forcément à user
})->scopeBindings();
```

---

**Q34. Comment grouper des routes avec des préfixes, middlewares et namespaces ?**

```php
Route::prefix('admin')
     ->name('admin.')
     ->middleware(['auth', 'role:admin'])
     ->group(function () {
         Route::get('/dashboard', DashboardController::class)->name('dashboard');
         Route::apiResource('utilisateurs', UtilisateurController::class);

         Route::prefix('rapports')->name('rapports.')->group(function () {
             Route::get('/ventes', RapportVentesController::class)->name('ventes');
         });
     });
```

---

## 5 — Eloquent ORM

**Q35. Expliquez les relations Eloquent et donnez des exemples.**

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

// Many-to-Many avec pivot
class Produit extends Model {
    public function categories(): BelongsToMany {
        return $this->belongsToMany(Categorie::class)
                    ->withPivot('ordre')
                    ->withTimestamps();
    }
}

// Has Many Through
class Pays extends Model {
    public function articles(): HasManyThrough {
        return $this->hasManyThrough(Article::class, Utilisateur::class);
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

// Many-to-Many polymorphique
class Tag extends Model {
    public function articles(): MorphedByMany {
        return $this->morphedByMany(Article::class, 'taggable');
    }
}
```

---

**Q36. Qu'est-ce que le problème N+1 en Eloquent ? Comment le résoudre ?**

Le problème N+1 survient quand une requête principale génère N requêtes supplémentaires.

```php
// ❌ N+1 : 1 requête pour articles + N requêtes pour chaque auteur
$articles = Article::all();
foreach ($articles as $article) {
    echo $article->auteur->nom; // requête par article !
}

// ✅ Eager loading : 2 requêtes seulement
$articles = Article::with('auteur')->get();

// ✅ Lazy eager loading
$articles = Article::all();
$articles->load('auteur');

// ✅ Conditional eager loading
$articles = Article::with([
    'auteur:id,nom',                          // colonnes spécifiques
    'commentaires' => fn($q) => $q->where('approuvé', true)->latest()->limit(5),
])->get();

// Détection
Model::preventLazyLoading(!app()->isProduction());
```

---

**Q37. Différence entre `get()`, `first()`, `find()`, `firstOrFail()` et `findOrFail()` ?**

```php
Article::get()                           // Collection de tous les articles
Article::first()                         // Premier article ou null
Article::find(1)                         // Article id=1 ou null
Article::find([1, 2, 3])                 // Collection des ids donnés
Article::firstOrFail()                   // Premier ou ModelNotFoundException
Article::findOrFail(1)                   // id=1 ou ModelNotFoundException
Article::firstOrCreate(['slug' => $s])   // cherche ou crée
Article::firstOrNew(['slug' => $s])      // cherche ou instancie (sans sauvegarder)
Article::updateOrCreate(['slug' => $s], ['titre' => $t])
```

---

**Q38. Qu'est-ce qu'un scope Eloquent ?**

```php
// Local scope
class Article extends Model {
    public function scopePublie(Builder $query): Builder {
        return $query->where('statut', 'publié');
    }

    public function scopeRecent(Builder $query, int $jours = 30): Builder {
        return $query->where('created_at', '>=', now()->subDays($jours));
    }
}

Article::publié()->recent(7)->get();

// Global scope
class ArticleScope implements Scope {
    public function apply(Builder $builder, Model $model): void {
        $builder->where('actif', true);
    }
}

// Ignorer
Article::withoutGlobalScope(ArticleScope::class)->get();
```

---

**Q39. Qu'est-ce que les Observers Eloquent ?**

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

**Q40. Comment fonctionne le soft delete en Eloquent ?**

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

**Q41. Qu'est-ce que les Casts Eloquent et les Custom Casts ?**

```php
class Article extends Model {
    protected $casts = [
        'publié_le'  => 'datetime',
        'options'    => 'array',          // JSON → array automatique
        'actif'      => 'boolean',
        'prix'       => 'decimal:2',
        'statut'     => Statut::class,    // Enum (PHP 8.1)
        'metadata'   => AsCollection::class,
    ];
}

// Custom Cast
class ArgentCast implements CastsAttributes {
    public function get(Model $model, string $key, mixed $value, array $attributes): Argent {
        return new Argent((int)$value, $attributes['devise'] ?? 'EUR');
    }

    public function set(Model $model, string $key, mixed $value, array $attributes): array {
        return ['montant' => $value->montant, 'devise' => $value->devise];
    }
}

class Produit extends Model {
    protected $casts = ['prix' => ArgentCast::class];
}
```

---

**Q42. Qu'est-ce que les Accessors et Mutators en Eloquent ?**

```php
class User extends Model {
    // Accessor (Laravel 9+ — méthode unifiée)
    protected function nomComplet(): Attribute {
        return Attribute::make(
            get: fn() => "{$this->prenom} {$this->nom}",
        );
    }

    // Mutator
    protected function password(): Attribute {
        return Attribute::make(
            set: fn(string $value) => Hash::make($value),
        );
    }

    // Avec cache de l'accessor
    protected function nomComplet(): Attribute {
        return Attribute::make(
            get: fn() => "{$this->prenom} {$this->nom}",
        )->shouldCache();
    }
}

echo $user->nom_complet;     // "Jean Dupont"
$user->password = 'secret';  // hashé automatiquement
```

---

**Q43. Comment utiliser les subqueries Eloquent avancées ?**

```php
// Sous-requête dans select
$users = User::select('*')
    ->addSelect([
        'dernier_login' => Connexion::select('created_at')
            ->whereColumn('user_id', 'users.id')
            ->latest()
            ->limit(1),
    ])
    ->get();

// Sous-requête dans orderBy
$articles = Article::orderByDesc(
    Commentaire::select('created_at')
        ->whereColumn('article_id', 'articles.id')
        ->latest()
        ->limit(1)
)->get();

// whereIn avec sous-requête
$articles = Article::whereIn('categorie_id',
    Categorie::where('actif', true)->select('id')
)->get();
```

---

## 6 — MVC & Blade

**Q44. Comment fonctionne l'architecture MVC dans Laravel ?**

- **Modèle** : représente les données (`app/Models`), interagit avec la BDD via Eloquent
- **Vue** : présentation HTML (`resources/views`), templates Blade
- **Contrôleur** : orchestre la requête (`app/Http/Controllers`), appelle les services/modèles

```
Requête → Routeur → Contrôleur → Service → Modèle → BDD
                        ↓
                      Vue ← données
```

---

**Q45. Principales directives Blade à connaître ?**

```blade
{{-- Affichage échappé (XSS safe) --}}
{{ $variable }}

{{-- Affichage non échappé --}}
{!! $html !!}

{{-- Conditions --}}
@if / @elseif / @else / @endif
@unless($condition) / @endunless
@isset($var) / @empty($var)
@switch($val) @case(1) ... @break @default @endswitch

{{-- Boucles --}}
@foreach / @forelse / @empty / @endforeach
@for / @while

// example
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

{{-- $loop dans @foreach --}}
$loop->index      // 0-based
$loop->iteration  // 1-based
$loop->first / $loop->last
$loop->count
$loop->parent     // dans une boucle imbriquée

{{-- Layouts --}}
@extends('layouts.app')
@section('content') ... @endsection
@yield('content')
@stack('scripts') / @push('scripts') ... @endpush

{{-- Composants --}}
<x-alerte type="success" :message="$msg">
    <x-slot:titre>Succès !</x-slot:titre>
</x-alerte>

{{-- Auth & Autorisation --}}
@auth / @guest
@can('modifier', $article) / @cannot
@role('admin')  // si package Spatie

{{-- Directives personnalisées --}}
@php ... @endphp
@verbatim ... @endverbatim
```

---

**Q46. Comment créer un composant Blade avec des props et slots ?**

```php
// php artisan make:component Alerte
// app/View/Components/Alerte.php

class Alerte extends Component {
    public function __construct(
        public string $type = 'info',
        public bool $dismissible = false,
    ) {}

    public function couleur(): string {
        return match($this->type) {
            'success' => 'green',
            'danger'  => 'red',
            'warning' => 'yellow',
            default   => 'blue',
        };
    }

    public function render(): View {
        return view('components.alerte');
    }
}
```

```blade
{{-- resources/views/components/alerte.blade.php --}}
<div class="alerte alerte-{{ $couleur() }}">
    @if($titre ?? false)
        <h4>{{ $titre }}</h4>
    @endif
    {{ $slot }}
    @if($dismissible)
        <button class="fermer">×</button>
    @endif
</div>

{{-- Utilisation --}}
<x-alerte type="success" :dismissible="true">
    <x-slot:titre>Bravo !</x-slot:titre>
    Votre article a été publié.
</x-alerte>
```

---

## 7 — API REST avec Laravel

**Q47. Comment créer une API REST versionnée dans Laravel ?**

```php
Route::prefix('v1')->name('v1.')->group(function () {
    Route::apiResource('articles', V1\ArticleController::class);
});

Route::prefix('v2')->name('v2.')->group(function () {
    Route::apiResource('articles', V2\ArticleController::class);
});
```

---

**Q48. Qu'est-ce qu'une API Resource dans Laravel ?**

```php
class ArticleResource extends JsonResource {
    public function toArray(Request $request): array {
        return [
            'id'        => $this->id,
            'titre'     => $this->titre,
            'auteur'    => new UserResource($this->whenLoaded('auteur')),
            'tags'      => TagResource::collection($this->whenLoaded('tags')),
            'publie_le' => $this->published_at?->format('d/m/Y'),
            // Attribut conditionnel
            'secret'    => $this->when($request->user()?->isAdmin(), $this->secret),
            'liens'     => [
                'self' => route('api.v1.articles.show', $this->id),
            ],
        ];
    }

    // Ajouter des métadonnées
    public function with(Request $request): array {
        return ['version' => 'v1'];
    }
}

// ResourceCollection personnalisée
class ArticleCollection extends ResourceCollection {
    public function toArray(Request $request): array {
        return [
            'data' => $this->collection,
            'meta' => ['version' => 'v1', 'total' => $this->total()],
        ];
    }
}
```

---

**Q49. Comment gérer l'authentification API avec Laravel Sanctum vs Passport ?**

| | Sanctum | Passport |
|---|---|---|
| Cas d'usage | SPA / Mobile simple | OAuth2 complet |
| Tokens | Personal Access Tokens | OAuth2 (auth code, client credentials...) |
| Complexité | Faible | Élevée |
| Tiers autorisés | ❌ | ✅ |
| Cookie session | ✅ (SPA) | ❌ |

```php
// Sanctum — Personal token
$token = $user->createToken('api', ['articles:read'])->plainTextToken;

// Sanctum — SPA (cookie-based, sans token)
// config/sanctum.php : stateful_domains

// Passport — Authorization Code
Route::get('/oauth/authorize', [AuthorizationController::class, 'authorize']);
```

---

**Q50. Comment implémenter la validation des requêtes API ?**

```php
class StoreArticleRequest extends FormRequest {
    public function authorize(): bool {
        return $this->user()->can('creer-article');
    }

    public function rules(): array {
        return [
            'titre'        => ['required', 'string', 'max:255'],
            'contenu'      => ['required', 'string', 'min:100'],
            'categorie_id' => ['required', 'exists:categories,id'],
            'tags'         => ['array', 'max:5'],
            'tags.*'       => ['string', 'exists:tags,nom'],
            'publie_le'    => ['nullable', 'date', 'after:today'],
        ];
    }

    public function messages(): array {
        return [
            'titre.required' => 'Le titre est obligatoire.',
            'contenu.min'    => 'Minimum :min caractères.',
        ];
    }

    // Transformer les données avant validation
    protected function prepareForValidation(): void {
        $this->merge([
            'slug' => Str::slug($this->titre),
        ]);
    }
}
```

Laravel retourne automatiquement une erreur `422 Unprocessable Entity` avec les erreurs en JSON pour les requêtes API (détectées via `Accept: application/json`).

---

**Q51. Comment gérer les erreurs API de façon centralisée dans Laravel 11 ?**

```php
// bootstrap/app.php
->withExceptions(function (Exceptions $exceptions) {
    $exceptions->render(function (ModelNotFoundException $e, Request $request) {
        if ($request->expectsJson()) {
            return response()->json(['message' => 'Ressource introuvable.'], 404);
        }
    });

    $exceptions->render(function (ValidationException $e, Request $request) {
        if ($request->expectsJson()) {
            return response()->json([
                'message' => 'Données invalides.',
                'errors'  => $e->errors(),
            ], 422);
        }
    });

    // Formater toutes les exceptions JSON
    $exceptions->shouldRenderJsonWhen(
        fn(Request $request, Throwable $e) => $request->expectsJson()
    );
})
```

---

## 8 — Artisan CLI

**Q52. Commandes Artisan essentielles.**

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
php artisan make:controller ArticleController --api --model=Article
php artisan make:request StoreArticleRequest
php artisan make:resource ArticleResource
php artisan make:middleware AuthToken
php artisan make:job EnvoyerEmail
php artisan make:event ArticlePublié
php artisan make:listener NotifierAbonnés --event=ArticlePublié
php artisan make:policy ArticlePolicy --model=Article
php artisan make:observer ArticleObserver --model=Article
php artisan make:rule TitreUnique
php artisan make:cast ArgentCast
php artisan make:scope ArticleActifScope
php artisan make:enum Statut

# Base de données
php artisan migrate
php artisan migrate:fresh --seed
php artisan migrate:rollback --step=3
php artisan db:seed --class=ArticleSeeder
php artisan tinker

# Queue
php artisan queue:work --queue=emails,default --tries=3
php artisan queue:failed
php artisan queue:retry all
php artisan horizon         # si Horizon installé

# Schedule
php artisan schedule:run
php artisan schedule:list

# Broadcasting
php artisan reverb:start    # Laravel Reverb (WebSocket)
```

---

**Q53. Comment créer une commande Artisan personnalisée ?**

```php
class EnvoyerRapportHebdo extends Command {
    protected $signature = 'rapport:hebdo
                            {--destinataire=admin@example.com}
                            {--semaine= : Numéro de semaine}
                            {--dry-run : Simuler sans envoyer}';

    protected $description = 'Envoie le rapport hebdomadaire des ventes';

    public function handle(RapportService $rapportService): int {
        $semaine = $this->option('semaine') ?? now()->weekOfYear;

        $this->info("Génération rapport semaine {$semaine}...");

        $bar = $this->output->createProgressBar(100);
        $bar->start();

        try {
            $rapport = $rapportService->generer($semaine);

            if (!$this->option('dry-run')) {
                Mail::to($this->option('destinataire'))->send(new RapportHebdo($rapport));
            }

            $bar->finish();
            $this->newLine();
            $this->info('✅ Rapport envoyé.');
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

Schedule::command('rapport:hebdo')->weeklyOn(1, '08:00')->emailOutputOnFailure('admin@app.com');
Schedule::command('cache:prune-stale-tags')->hourly();
```

---

## 9 — PHPUnit & Tests

**Q54. Différence entre test unitaire, d'intégration et fonctionnel dans Laravel.**

| Type | Ce qu'il teste | Outils |
|---|---|---|
| Unitaire | Une classe/méthode isolée | PHPUnit, Mockery |
| Intégration | Interaction entre composants (BDD, services) | PHPUnit + SQLite in-memory |
| Fonctionnel (Feature) | Une fonctionnalité HTTP complète | `$this->get()`, `actingAs()` |
| E2E (bout en bout) | Parcours utilisateur complet | Laravel Dusk + Chrome |

---

**Q55. Comment écrire un test de feature HTTP dans Laravel ?**

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

    public function test_la_validation_echoue_sans_titre(): void {
        $user = User::factory()->create();

        $this->actingAs($user)
             ->postJson('/api/v1/articles', [])
             ->assertUnprocessable()
             ->assertJsonValidationErrors(['titre', 'contenu']);
    }
}
```

---

**Q56. Comment mocker des dépendances dans les tests Laravel ?**

```php
// Fakes de Facades
Mail::fake();
Storage::fake('s3');
Queue::fake();
Event::fake([ArticlePublié::class]);
Notification::fake();
Http::fake(['api.stripe.com/*' => Http::response(['id' => 'ch_xxx'], 200)]);

// Assertions après action
Mail::assertSent(ConfirmationCommande::class, fn($mail) =>
    $mail->hasTo($commande->client->email)
);
Queue::assertPushed(EnvoyerEmail::class, 2); // 2 fois
Event::assertDispatched(ArticlePublié::class, fn($e) => $e->article->id === 1);

// Mock via container
$this->mock(PaiementService::class, function (MockInterface $mock) {
    $mock->shouldReceive('payer')->with(99.99)->once()->andReturn(true);
});

// Spy (enregistre les appels sans bloquer)
$this->spy(AnalyticsService::class);
```

---

**Q57. Qu'est-ce qu'un Factory en Laravel ?**

```php
class ArticleFactory extends Factory {
    public function definition(): array {
        return [
            'titre'     => fake()->sentence(),
            'slug'      => fake()->unique()->slug(),
            'contenu'   => fake()->paragraphs(3, true),
            'statut'    => fake()->randomElement(['brouillon', 'publié']),
            'auteur_id' => User::factory(),
            'publié_le' => fake()->optional()->dateTimeThisYear(),
        ];
    }

    public function publié(): static {
        return $this->state(['statut' => 'publié', 'publié_le' => now()]);
    }

    public function avecCommentaires(int $n = 3): static {
        return $this->has(Commentaire::factory()->count($n));
    }
}

// Utilisation
Article::factory()->count(5)->publié()->avecCommentaires()->create();
Article::factory()->for($user, 'auteur')->sequence(
    ['titre' => 'Premier article'],
    ['titre' => 'Deuxième article'],
)->create();
```

---

**Q58. Comment tester les Jobs, Events et Notifications ?**

```php
// Tester un Job directement
public function test_job_envoie_email(): void {
    Mail::fake();

    $user = User::factory()->create();
    (new EnvoyerEmailBienvenue($user))->handle();

    Mail::assertSent(EmailBienvenue::class, fn($m) => $m->hasTo($user->email));
}

// Tester qu'un job est mis en queue
public function test_commande_dispatche_un_job(): void {
    Queue::fake();

    $this->postJson('/api/commandes', $données);

    Queue::assertPushed(TraiterCommande::class, fn($job) =>
        $job->commande->montant === 99.99
    );
}

// Tester une notification
public function test_utilisateur_reçoit_notification(): void {
    Notification::fake();

    $user = User::factory()->create();
    $user->notify(new FactureDisponible($facture));

    Notification::assertSentTo($user, FactureDisponible::class, fn($n) =>
        $n->facture->id === $facture->id
    );
}
```

---

**Q59. Comment tester les politiques d'autorisation (Policies) ?**

```php
public function test_auteur_peut_modifier_son_article(): void {
    $auteur = User::factory()->create();
    $article = Article::factory()->for($auteur, 'auteur')->create();

    $this->actingAs($auteur)
         ->putJson("/api/articles/{$article->id}", ['titre' => 'Nouveau titre'])
         ->assertOk();
}

public function test_autre_utilisateur_ne_peut_pas_modifier(): void {
    $auteur    = User::factory()->create();
    $intrus    = User::factory()->create();
    $article   = Article::factory()->for($auteur, 'auteur')->create();

    $this->actingAs($intrus)
         ->putJson("/api/articles/{$article->id}", ['titre' => 'Hack'])
         ->assertForbidden();
}
```

---

## 10 — MySQL & Base de Données

**Q60. Comment optimiser une requête SQL lente dans Laravel ?**

```php
// 1. Activer le query log
DB::enableQueryLog();
// ... requêtes ...
dd(DB::getQueryLog());

// 2. EXPLAIN
DB::select('EXPLAIN SELECT * FROM articles WHERE statut = ?', ['publié']);

// 3. Index composite
Schema::table('articles', function (Blueprint $table) {
    $table->index(['statut', 'published_at']);
    $table->fullText(['titre', 'contenu']);
});

// 4. Limiter les colonnes
Article::select('id', 'titre', 'auteur_id')->with('auteur:id,nom')->get();

// 5. Chunk pour les gros volumes
Article::chunk(500, fn($articles) => ProcessArticle::dispatch($articles));

// 6. LazyCollection pour streaming
Article::lazy()->each(fn($article) => traiter($article));

// 7. Cursor (yield par ligne)
foreach (Article::cursor() as $article) {
    traiter($article);
}
```

---

**Q61. Expliquez les transactions de base de données.**

```php
// Callback automatique (recommandé)
DB::transaction(function () use ($commande, $items) {
    $commande = Commande::create($commande);
    foreach ($items as $item) {
        $commande->lignes()->create($item);
        Stock::decrementer($item['produit_id'], $item['quantite']);
    }
}, attempts: 3); // retry en cas de deadlock

// Manuelle
DB::beginTransaction();
try {
    DB::commit();
} catch (\Throwable $e) {
    DB::rollBack();
    throw $e;
}

// Niveaux d'isolation
DB::statement('SET TRANSACTION ISOLATION LEVEL SERIALIZABLE');
```

---

**Q62. Différence entre `whereHas` et `with` en Eloquent ?**

```php
// with() : charge la relation sans filtrer les parents
$articles = Article::with('commentaires')->get();
// Retourne TOUS les articles avec leurs commentaires

// whereHas() : filtre les parents selon l'existence d'une relation
$articles = Article::whereHas('commentaires', fn($q) =>
    $q->where('approuvé', true)
)->get();
// Retourne UNIQUEMENT les articles avec au moins 1 commentaire approuvé

// doesntHave / whereDoesntHave
$articles = Article::doesntHave('commentaires')->get();

// withCount
$articles = Article::withCount(['commentaires', 'tags'])->get();
echo $articles->first()->commentaires_count;
```

---

**Q63. Qu'est-ce que l'indexation MySQL ?**

Créer un index quand : colonne dans `WHERE`, `ORDER BY`, `GROUP BY`, jointures.

```sql
-- Index simple
CREATE INDEX idx_statut ON articles(statut);

-- Index composite (du plus sélectif au moins)
CREATE INDEX idx_statut_date ON articles(statut, published_at);

-- Index unique
CREATE UNIQUE INDEX idx_slug ON articles(slug);

-- Full-text
CREATE FULLTEXT INDEX ft_contenu ON articles(titre, contenu);

-- Index sur expression (MySQL 8+)
CREATE INDEX idx_lower_email ON users((LOWER(email)));
```

Éviter d'indexer : colonnes booléennes, tables petites, colonnes rarement filtrées.

---

**Q64. Comment fonctionne la pagination dans Laravel ?**

```php
// Pagination classique (avec liens)
$articles = Article::paginate(15);                  // ?page=2
$articles = Article::simplePaginate(15);            // uniquement prev/next
$articles = Article::cursorPaginate(15);            // pagination par curseur (performante)

// En API (JSON)
return ArticleResource::collection(Article::paginate(15));
// Inclut automatiquement links et meta

// Pagination personnalisée
$articles = Article::paginate(15, ['id', 'titre'], 'p', $request->p);
```

---

**Q65. Qu'est-ce que les migrations et les Seeders ? Bonnes pratiques.**

```php
// Migration atomique et réversible
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

// Seeder
class ArticleSeeder extends Seeder {
    public function run(): void {
        Article::factory()
            ->count(50)
            ->publié()
            ->for(User::factory()->create(['email' => 'admin@app.com']), 'auteur')
            ->create();
    }
}
```

---

## 11 — MongoDB avec Laravel

**Q66. Comment intégrer MongoDB dans Laravel ?**

```bash
# Installation
composer require mongodb/laravel-mongodb

# Extension PHP requise
pecl install mongodb
```

```php
// config/database.php
'connections' => [
    'mongodb' => [
        'driver'   => 'mongodb',
        'dsn'      => env('MONGODB_DSN', 'mongodb://localhost:27017'),
        'database' => env('MONGODB_DATABASE', 'app'),
    ],
],
```

---

**Q67. Comment créer un modèle Eloquent compatible MongoDB ?**

```php
use MongoDB\Laravel\Eloquent\Model;

class Produit extends Model {
    protected $connection = 'mongodb';
    protected $collection = 'produits';

    protected $fillable = [
        'nom', 'prix', 'categories', 'specifications', 'stock',
    ];

    protected $casts = [
        'categories'     => 'array',
        'specifications' => 'array',
        'prix'           => 'decimal:2',
        'actif'          => 'boolean',
    ];
}

// Les relations Eloquent classiques fonctionnent
class Commande extends Model {
    public function produits(): HasMany {
        return $this->hasMany(Produit::class);
    }
}
```

---

**Q68. Comment effectuer des requêtes MongoDB avancées avec Laravel ?**

```php
// Requêtes de base
$produits = Produit::where('prix', '>', 100)->get();

// Requêtes sur sous-documents (embedded)
Produit::where('adresse.ville', 'Paris')->get();

// Requêtes sur tableaux
Produit::where('categories', 'electronics')->get();
Produit::whereIn('categories', ['electronics', 'informatique'])->get();

// Opérateurs MongoDB
Produit::where('stock', 'elemMatch', ['taille' => 'L', 'quantite' => ['$gt' => 0]])->get();

// Aggregation pipeline
Produit::raw(function($collection) {
    return $collection->aggregate([
        ['$match'  => ['actif' => true]],
        ['$group'  => [
            '_id'        => '$categorie',
            'total'      => ['$sum' => '$prix'],
            'moyenne'    => ['$avg' => '$prix'],
            'nb_produits'=> ['$count' => new \stdClass()],
        ]],
        ['$sort'   => ['total' => -1]],
        ['$limit'  => 10],
    ]);
})->toArray();

// Full-text search (index Atlas Search ou text index)
Produit::where('$text', ['$search' => 'laptop gaming'])->get();

// Geospatial
Restaurant::where('localisation', 'near', [
    '$geometry'    => ['type' => 'Point', 'coordinates' => [2.3522, 48.8566]],
    '$maxDistance' => 1000, // mètres
])->get();
```

---

**Q69. Comment gérer les documents imbriqués (embedded documents) dans MongoDB ?**

```php
// Document imbriqué vs référence — choix stratégique :
// - Imbriqué : lu souvent ensemble, cardinalité faible (adresses, options)
// - Référence : entités autonomes, cardinalité élevée (commandes, utilisateurs)

class Utilisateur extends Model {
    // Adresses imbriquées
    protected $casts = [
        'adresses' => 'array',
    ];

    public function ajouterAdresse(array $adresse): void {
        $this->push('adresses', $adresse);
    }

    public function supprimerAdresse(string $id): void {
        $this->pull('adresses', ['id' => $id]);
    }
}

// Opérateurs d'update spécifiques MongoDB
$user->push('tags', 'nouveau-tag');          // $push
$user->pull('tags', 'ancien-tag');           // $pull
$user->increment('connexions', 1);           // $inc
Produit::where('_id', $id)->unset('champ'); // $unset
```

---

**Q70. Quelles sont les différences fondamentales entre SQL et MongoDB ?**

| Concept | SQL (MySQL) | MongoDB |
|---|---|---|
| Structure | Tables, colonnes, lignes | Collections, documents (BSON) |
| Schéma | Fixe (rigid) | Flexible (schemaless) |
| Relations | JOINs | Référence ou embedding |
| Transactions | ACID complet | ACID depuis 4.0 (multi-documents) |
| Indexation | B-tree, Full-text | B-tree, Geo, Text, Wildcard, Atlas |
| Scalabilité | Verticale (+ RAM) | Horizontale (sharding natif) |
| Requêtes | SQL | MQL (MongoDB Query Language) |
| Cas d'usage | Relations complexes, reporting | Hiérarchique, temps réel, IoT |

---

**Q71. Comment gérer les transactions MongoDB dans Laravel ?**

```php
// Transactions multi-documents (MongoDB 4.0+, replica set requis)
use MongoDB\Laravel\Facades\MongoDB;

MongoDB::transaction(function () use ($commande, $produits) {
    $cmd = Commande::create([
        'client_id' => $commande['client_id'],
        'total'     => $commande['total'],
        'statut'    => 'en_attente',
    ]);

    foreach ($produits as $produit) {
        Produit::where('_id', $produit['id'])
               ->decrement('stock', $produit['quantite']);

        if (Produit::find($produit['id'])->stock < 0) {
            throw new \Exception("Stock insuffisant");
        }
    }

    $cmd->update(['statut' => 'confirmée']);
});
```

---

**Q72. Comment créer des index MongoDB efficacement ?**

```php
// Via migration MongoDB
use MongoDB\Laravel\Schema\Blueprint;

Schema::connection('mongodb')->create('produits', function (Blueprint $collection) {
    $collection->index('categorie');
    $collection->unique('sku');
    $collection->index([
        'localisation' => '2dsphere',   // géospatial
    ]);
    $collection->index([
        'nom'         => 'text',
        'description' => 'text',
    ]);                                  // full-text
    $collection->index(
        ['created_at' => 1],
        ['expireAfterSeconds' => 86400]  // TTL index
    );
});

// TTL index — documents expirés automatiquement (sessions, logs)
// Wildcard index — indexe tous les champs d'un sous-document
// Partial index — indexe seulement les documents qui correspondent à un filtre
```

---

## 12 — Redis avec Laravel

**Q73. Comment configurer et utiliser Redis avec Laravel ?**

```bash
composer require predis/predis
# ou extension phpredis (plus performant)
```

```php
// config/database.php
'redis' => [
    'client'  => env('REDIS_CLIENT', 'phpredis'),
    'default' => [
        'url'      => env('REDIS_URL'),
        'host'     => env('REDIS_HOST', '127.0.0.1'),
        'password' => env('REDIS_PASSWORD'),
        'port'     => env('REDIS_PORT', '6379'),
        'database' => env('REDIS_DB', '0'),
    ],
    'cache' => [
        'url'      => env('REDIS_URL'),
        'host'     => env('REDIS_HOST', '127.0.0.1'),
        'password' => env('REDIS_PASSWORD'),
        'port'     => env('REDIS_PORT', '6379'),
        'database' => env('REDIS_CACHE_DB', '1'), // DB séparée pour le cache
    ],
],
```

```env
CACHE_DRIVER=redis
QUEUE_CONNECTION=redis
SESSION_DRIVER=redis
```

---

**Q74. Quelles sont les structures de données Redis et leurs usages ?**

```php
use Illuminate\Support\Facades\Redis;

// String — cache simple, compteurs
Redis::set('user:1:nom', 'Jean');
Redis::get('user:1:nom');
Redis::incr('compteur:visites');
Redis::expire('user:1:token', 3600);

// Hash — objet structuré
Redis::hset('user:1', 'nom', 'Jean', 'email', 'jean@app.com');
Redis::hget('user:1', 'nom');
Redis::hgetall('user:1');
Redis::hmset('user:1', ['nom' => 'Jean', 'age' => 30]);

// List — file FIFO/LIFO, activité récente
Redis::lpush('notifications:1', json_encode($notif)); // push gauche
Redis::rpop('notifications:1');                        // pop droite
Redis::lrange('notifications:1', 0, 9);               // 10 dernières

// Set — unicité, tags
Redis::sadd('article:1:vues', $userId);
Redis::sismember('article:1:vues', $userId);
Redis::smembers('article:1:tags');
Redis::sinter('tags:php', 'tags:laravel');              // intersection

// Sorted Set — classements, leaderboards
Redis::zadd('leaderboard', $score, $userId);
Redis::zrevrange('leaderboard', 0, 9, 'WITHSCORES'); // top 10
Redis::zincrby('leaderboard', 10, $userId);           // ajouter des points

// HyperLogLog — comptage approximatif unique (mémoire constante)
Redis::pfadd('visiteurs:2024-01', $ip);
Redis::pfcount('visiteurs:2024-01'); // ≈ nombre visiteurs uniques

// Pub/Sub
Redis::subscribe(['notifications'], function ($message, $channel) {
    echo "Reçu sur {$channel}: {$message}";
});
Redis::publish('notifications', json_encode($données));
```

---

**Q75. Comment utiliser Redis comme système de cache dans Laravel ?**

```php
// Cache simple
Cache::put('articles:accueil', $articles, 3600);
$articles = Cache::get('articles:accueil', fn() => $this->charger());

// Cache remember (atomique)
$articles = Cache::remember('articles:accueil', now()->addHour(), function () {
    return Article::publié()->with('auteur')->latest()->take(10)->get();
});

// Cache permanent
Cache::forever('config:app', $config);

// Tags (invalider un groupe de clés — Redis uniquement)
Cache::tags(['articles', 'homepage'])->put('featured', $data, 3600);
Cache::tags(['articles'])->flush(); // invalide tous les caches taggés 'articles'

// Atomic lock (éviter les race conditions)
$verrou = Cache::lock('traitement:commande:' . $commandeId, 10);
if ($verrou->get()) {
    try {
        traiterCommande($commandeId);
    } finally {
        $verrou->release();
    }
}

// Cache par store nommé
Cache::store('redis')->put('clé', 'valeur', 3600);
Cache::store('memcached')->get('clé');
```

---

**Q76. Comment utiliser Redis pour les sessions dans Laravel ?**

```php
// .env
SESSION_DRIVER=redis
SESSION_LIFETIME=120 // minutes

// config/session.php
'connection'  => 'redis',
'cookie'      => env('SESSION_COOKIE', Str::slug(env('APP_NAME', 'laravel'), '_') . '_session'),
'secure'      => env('SESSION_SECURE_COOKIE', true),
'same_site'   => 'lax',
'http_only'   => true,

// Utilisation
session(['clé' => 'valeur']);
session('clé');
session()->forget('clé');
session()->flush();
session()->regenerate(); // sécurité après login

// Flash (disponible seulement pour la prochaine requête)
session()->flash('succès', 'Article publié !');
session()->reflash(); // conserver le flash une requête de plus
```

---

**Q77. Comment utiliser Redis pour les files d'attente (Queues) ?**

```php
// .env
QUEUE_CONNECTION=redis

// Dispatcher
EnvoyerEmailBienvenue::dispatch($user)->onQueue('emails');
EnvoyerEmailBienvenue::dispatch($user)->delay(now()->addMinutes(5));

// Worker
// php artisan queue:work redis --queue=high,emails,default --tries=3 --timeout=60

// Laravel Horizon (monitoring visuel)
composer require laravel/horizon
php artisan horizon:install
php artisan horizon

// config/horizon.php
'environments' => [
    'production' => [
        'supervisor-1' => [
            'connection'  => 'redis',
            'queue'       => ['high', 'default', 'emails'],
            'balance'     => 'auto',
            'minProcesses'=> 1,
            'maxProcesses'=> 10,
        ],
    ],
],
```

---

**Q78. Comment implémenter un Rate Limiter personnalisé avec Redis ?**

```php
class RateLimiterRedis {
    public function __construct(private Redis $redis) {}

    public function tenterAccès(string $clé, int $max, int $fenetre): bool {
        $pipeline = $this->redis->pipeline();
        $pipeline->incr($clé);
        $pipeline->expire($clé, $fenetre);
        $résultats = $pipeline->exec();

        return $résultats[0] <= $max;
    }
}

// Sliding window avec Sorted Set
public function sliding(string $clé, int $max, int $fenetre): bool {
    $maintenant  = microtime(true);
    $windowStart = $maintenant - $fenetre;

    Redis::pipeline(function ($pipe) use ($clé, $maintenant, $windowStart) {
        $pipe->zremrangebyscore($clé, '-inf', $windowStart);
        $pipe->zadd($clé, $maintenant, $maintenant);
        $pipe->expire($clé, (int)ceil($fenetre));
    });

    return Redis::zcard($clé) <= $max;
}
```

---

**Q79. Comment utiliser Redis Pub/Sub pour le broadcasting en temps réel ?**

```php
// Événement broadcasté
class CommandeStatutChangé implements ShouldBroadcast {
    public function __construct(public Commande $commande) {}

    public function broadcastOn(): array {
        return [
            new PrivateChannel("commandes.{$this->commande->client_id}"),
        ];
    }

    public function broadcastWith(): array {
        return [
            'statut'     => $this->commande->statut,
            'message'    => "Votre commande est {$this->commande->statut}",
            'updated_at' => $this->commande->updated_at->toIso8601String(),
        ];
    }
}

// config/broadcasting.php
'connections' => [
    'redis' => [
        'driver'     => 'redis',
        'connection' => 'default',
    ],
    'reverb' => [ // Laravel Reverb (WebSocket natif Laravel)
        'driver'   => 'reverb',
        'app_id'   => env('REVERB_APP_ID'),
        'app_key'  => env('REVERB_APP_KEY'),
        'app_secret' => env('REVERB_APP_SECRET'),
    ],
],
```

---

## 13 — Inertia.js avec Laravel

**Q80. Qu'est-ce qu'Inertia.js et pourquoi l'utiliser avec Laravel ?**

Inertia.js est un protocole qui permet de créer des SPA (Single Page Applications) en utilisant les frameworks serveur existants (Laravel, Rails) sans construire une API séparée. Il sert de "colle" entre Laravel et Vue/React/Svelte.

**Avantages vs API REST classique :**
- Pas besoin de gérer l'authentification JWT côté client
- Pas de duplication de logique de validation
- Routing côté serveur (Laravel Router)
- Moins de code boilerplate

**Avantages vs Blade classique :**
- Navigation sans rechargement de page
- Composants réactifs Vue/React
- Meilleure expérience utilisateur

---

**Q81. Comment installer et configurer Inertia.js avec Laravel et Vue 3 ?**

```bash
# Backend
composer require inertiajs/inertia-laravel
php artisan inertia:middleware

# Frontend
npm install @inertiajs/vue3 vue
npm install --save-dev @vitejs/plugin-vue
```

```php
// app/Http/Kernel.php — ajouter le middleware
'web' => [
    // ...
    \App\Http\Middleware\HandleInertiaRequests::class,
],

// app/Http/Middleware/HandleInertiaRequests.php
class HandleInertiaRequests extends Middleware {
    protected $rootView = 'app';

    // Données partagées avec toutes les pages
    public function share(Request $request): array {
        return array_merge(parent::share($request), [
            'auth' => [
                'user' => $request->user()?->only('id', 'nom', 'email', 'avatar'),
            ],
            'flash' => [
                'success' => fn() => $request->session()->get('success'),
                'error'   => fn() => $request->session()->get('error'),
            ],
        ]);
    }
}
```

```blade
{{-- resources/views/app.blade.php --}}
<!DOCTYPE html>
<html>
<head>
    @vite(['resources/css/app.css', 'resources/js/app.js'])
    @inertiaHead
</head>
<body>
    @inertia
</body>
</html>
```

```javascript
// resources/js/app.js
import { createApp, h } from 'vue'
import { createInertiaApp } from '@inertiajs/vue3'

createInertiaApp({
    resolve: name => {
        const pages = import.meta.glob('./Pages/**/*.vue', { eager: true })
        return pages[`./Pages/${name}.vue`]
    },
    setup({ el, App, props, plugin }) {
        createApp({ render: () => h(App, props) })
            .use(plugin)
            .mount(el)
    },
})
```

---

**Q82. Comment faire des liens et naviguer avec Inertia.js ?**

```vue
<script setup>
import { Link, router } from '@inertiajs/vue3'
import { ref } from 'vue'

// Navigation programmatique
const allerDashboard = () => {
    router.visit('/dashboard')
}

// GET avec paramètres
const rechercher = (query) => {
    router.get('/articles', { q: query }, {
        preserveState: true,    // ne pas réinitialiser les données de la page
        preserveScroll: true,   // garder la position de scroll
        replace: true,          // remplacer l'historique au lieu d'ajouter
    })
}

// POST
const soumettre = (données) => {
    router.post('/articles', données, {
        onSuccess: () => console.log('Succès'),
        onError: (errors) => console.log(errors),
        onFinish: () => console.log('Terminé'),
    })
}
</script>

<template>
    <!-- Lien Inertia (SPA, sans rechargement) -->
    <Link href="/articles" class="nav-link">Articles</Link>
    <Link :href="route('articles.show', article.id)" method="get">Voir</Link>

    <!-- Lien de suppression (POST/DELETE) -->
    <Link :href="`/articles/${article.id}`" method="delete"
          as="button" type="button">
        Supprimer
    </Link>
</template>
```

---

**Q83. Comment gérer les formulaires avec Inertia.js ?**

```vue
<script setup>
import { useForm } from '@inertiajs/vue3'

const form = useForm({
    titre:   '',
    contenu: '',
    tags:    [],
    image:   null,
})

const soumettre = () => {
    form.post('/articles', {
        forceFormData: true, // pour les uploads de fichiers
        onSuccess: () => form.reset(),
    })
}

// Équivalent à transform pour modifier les données avant envoi
const enregistrer = () => {
    form.transform(données => ({
        ...données,
        slug: données.titre.toLowerCase().replace(/ /g, '-'),
    })).post('/articles')
}
</script>

<template>
    <form @submit.prevent="soumettre">
        <input v-model="form.titre" type="text" />
        <span v-if="form.errors.titre" class="erreur">{{ form.errors.titre }}</span>

        <textarea v-model="form.contenu"></textarea>
        <span v-if="form.errors.contenu" class="erreur">{{ form.errors.contenu }}</span>

        <input type="file" @change="form.image = $event.target.files[0]" />

        <button type="submit" :disabled="form.processing">
            {{ form.processing ? 'Envoi...' : 'Publier' }}
        </button>
    </form>
</template>
```

---

**Q84. Comment partager des données globales avec Inertia (Shared Props) ?**

```php
// HandleInertiaRequests.php
public function share(Request $request): array {
    return [
        ...parent::share($request),
        'auth' => [
            'user'        => $request->user(),
            'permissions' => $request->user()?->getAllPermissions()->pluck('name'),
        ],
        'flash'      => [
            'success' => fn() => $request->session()->get('success'),
            'error'   => fn() => $request->session()->get('error'),
        ],
        'config' => [
            'appName' => config('app.name'),
            'locale'  => app()->getLocale(),
        ],
    ];
}
```

```vue
<!-- Accès dans n'importe quel composant -->
<script setup>
import { usePage } from '@inertiajs/vue3'
import { computed } from 'vue'

const page = usePage()
const user = computed(() => page.props.auth.user)
const flash = computed(() => page.props.flash)
</script>

<template>
    <div v-if="flash.success" class="alert alert-success">
        {{ flash.success }}
    </div>
    <p>Connecté : {{ user?.nom }}</p>
</template>
```

---

**Q85. Comment gérer l'authentification et l'autorisation avec Inertia ?**

```php
// Retourner les permissions au frontend
public function share(Request $request): array {
    return [
        'auth' => [
            'user' => $request->user(),
            'can'  => [
                'creer_article'    => $request->user()?->can('create', Article::class),
                'voir_dashboard'   => $request->user()?->can('viewAny', User::class),
            ],
        ],
    ];
}

// Contrôleur — redirect avec message flash
public function destroy(Article $article): RedirectResponse {
    $this->authorize('delete', $article);
    $article->delete();
    return redirect()->route('articles.index')
                     ->with('success', 'Article supprimé.');
}
```

```vue
<script setup>
import { usePage } from '@inertiajs/vue3'

const can = usePage().props.auth.can
</script>

<template>
    <Link v-if="can.creer_article" href="/articles/create">Nouvel article</Link>
</template>
```

---

**Q86. Comment faire de la pagination avec Inertia.js ?**

```php
// Contrôleur
public function index(): Response {
    return Inertia::render('Articles/Index', [
        'articles' => ArticleResource::collection(
            Article::with('auteur')
                   ->latest()
                   ->paginate(15)
                   ->withQueryString() // préserve les filtres dans les liens
        ),
        'filtres' => request()->only(['recherche', 'statut', 'categorie']),
    ]);
}
```

```vue
<script setup>
import { Link } from '@inertiajs/vue3'

const props = defineProps({
    articles: Object, // { data, links, meta }
    filtres:  Object,
})
</script>

<template>
    <div v-for="article in articles.data" :key="article.id">
        {{ article.titre }}
    </div>

    <!-- Liens de pagination Inertia -->
    <div class="pagination">
        <Link v-for="link in articles.links" :key="link.label"
              :href="link.url || ''"
              :class="{ 'active': link.active, 'disabled': !link.url }"
              v-html="link.label"
              preserve-scroll
        />
    </div>

    <p>Affichage {{ articles.meta.from }}–{{ articles.meta.to }}
       sur {{ articles.meta.total }}</p>
</template>
```

---

**Q87. Comment gérer les uploads de fichiers avec Inertia ?**

```vue
<script setup>
import { useForm } from '@inertiajs/vue3'
import { ref } from 'vue'

const form = useForm({
    nom:    '',
    avatar: null,
})

const aperçu = ref(null)

const onFichierChange = (e) => {
    const fichier = e.target.files[0]
    form.avatar = fichier
    aperçu.value = URL.createObjectURL(fichier)
}

const soumettre = () => {
    form.post('/profil', {
        forceFormData: true,
        onSuccess: () => {
            URL.revokeObjectURL(aperçu.value)
            aperçu.value = null
        },
    })
}
</script>
```

```php
// Contrôleur
public function update(Request $request): RedirectResponse {
    $request->validate([
        'avatar' => ['image', 'max:2048', 'mimes:jpg,jpeg,png,webp'],
    ]);

    $chemin = $request->file('avatar')->store('avatars', 's3');
    auth()->user()->update(['avatar' => $chemin]);

    return redirect()->back()->with('success', 'Avatar mis à jour.');
}
```

---

**Q88. Différence entre Inertia.js, Livewire et une API + SPA classique ?**

| | Inertia.js | Livewire | API + SPA (Vue/React) |
|---|---|---|---|
| Paradigme | Protocole d'adaptateur | Composants PHP réactifs | API séparée + frontend |
| Routing | Serveur (Laravel) | Serveur (Laravel) | Client (Vue Router) |
| État | Props Inertia | Props Livewire | Store (Pinia/Redux) |
| Auth | Session Laravel | Session Laravel | Token (JWT/Sanctum) |
| Temps réel | Via Broadcasting | Polling / Alpine | WebSocket |
| Courbe d'apprentissage | Faible | Faible | Élevée |
| Idéal pour | Apps Laravel + SPA | Composants interactifs | Apps découplées |

---

## 14 — Sécurité

**Q89. Comment Laravel protège-t-il contre les injections SQL ?**

```php
// ✅ Requêtes préparées (automatique avec Eloquent)
User::where('email', $request->email)->first();
DB::select('SELECT * FROM users WHERE email = ?', [$email]);

// ❌ Jamais d'interpolation directe
DB::select("SELECT * FROM users WHERE email = '{$email}'");

// Whitelist pour les colonnes dynamiques
$colonne = in_array($request->sort, ['nom', 'email', 'created_at'])
    ? $request->sort : 'created_at';
```

---

**Q90. Comment fonctionne la protection CSRF dans Laravel ?**

Laravel génère un token CSRF unique par session. Pour les formulaires Blade, la directive `@csrf` insère un champ caché. Le middleware `VerifyCsrfToken` valide le token à chaque requête `POST/PUT/PATCH/DELETE`.

```blade
<form method="POST" action="/articles">
    @csrf
</form>
```

Pour les API AJAX :
```javascript
axios.defaults.headers.common['X-CSRF-TOKEN'] =
    document.querySelector('meta[name="csrf-token"]').content;
```

Les routes API (`routes/api.php`) sont exemptées par défaut.

---

**Q91. Comment stocker les mots de passe en toute sécurité ?**

```php
// Hashing bcrypt (défaut) ou argon2id
$user->password = Hash::make($request->password);

// Vérification
Hash::check($request->password, $user->password);

// Rehash si le coût change
if (Hash::needsRehash($user->password)) {
    $user->update(['password' => Hash::make($plaintext)]);
}

// Jamais : MD5, SHA1, base64, en clair
```

---

**Q92. Comment implémenter l'autorisation avec les Policies ?**

```php
class ArticlePolicy {
    public function update(User $user, Article $article): bool {
        return $user->id === $article->auteur_id || $user->isAdmin();
    }

    public function delete(User $user, Article $article): Response {
        return $user->id === $article->auteur_id
            ? Response::allow()
            : Response::deny('Vous ne pouvez supprimer que vos articles.');
    }
}

// Contrôleur
$this->authorize('update', $article);

// Blade
@can('update', $article) ... @endcan

// Gate
Gate::allows('update', $article);
Gate::any(['update', 'delete'], $article); // au moins une
```

---

**Q93. Comment sécuriser une API Laravel contre les attaques courantes ?**

```php
// XSS — Blade échappe automatiquement {{ $var }}
// CSRF — middleware automatique sur routes web
// SQL Injection — Eloquent / Query Builder avec bindings

// Sécurité des headers HTTP (via middleware ou package)
// composer require bepsvpt/secure-headers
// Définit : X-Frame-Options, X-Content-Type-Options, CSP, HSTS...

// Validation stricte des entrées
$request->validate([
    'email'    => ['required', 'email:strict,dns'],
    'url'      => ['url', 'active_url'],
    'fichier'  => ['mimes:pdf,docx', 'max:5120', 'file'],
]);

// Masquer les erreurs en production
// APP_DEBUG=false
// LOG_CHANNEL=stack (pas stderr)

// Chiffrement des données sensibles
$chiffré = Crypt::encryptString($numéroCarteVisa);
$déchiffré = Crypt::decryptString($chiffré);

// Rate limiting sur les endpoints sensibles
RateLimiter::for('connexion', fn(Request $r) =>
    Limit::perMinute(5)->by($r->ip())
);
```

---

**Q94. Qu'est-ce que le Mass Assignment et comment s'en protéger ?**

```php
// Problème : remplissage de champs non autorisés
$user = User::create($request->all()); // DANGEREUX si is_admin dans la requête

// ✅ Solution 1 : $fillable (whitelist)
class User extends Model {
    protected $fillable = ['nom', 'email', 'password'];
    // 'is_admin' non fillable → ignoré
}

// ✅ Solution 2 : $guarded (blacklist)
class Article extends Model {
    protected $guarded = ['id', 'auteur_id', 'validé'];
}

// ✅ Solution 3 : validated() au lieu de all()
User::create($request->validated());     // seulement les champs validés
User::create($request->only(['nom', 'email', 'password']));
```

---

## 15 — Performance & Optimisation

**Q95. Comment utiliser le cache dans Laravel ?**

```php
Cache::put('articles:une', $article, now()->addHour());

$articles = Cache::remember('articles:accueil', 3600, function () {
    return Article::publié()->with('auteur')->latest()->take(10)->get();
});

Cache::forever('config:generale', $config);
Cache::forget('articles:accueil');
Cache::tags(['articles'])->flush(); // Redis uniquement
```

---

**Q96. Qu'est-ce que les files d'attente (Queues) dans Laravel ?**

```php
class EnvoyerEmailBienvenue implements ShouldQueue {
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public int $tries   = 3;
    public int $timeout = 60;
    public int $backoff = 30;

    public function __construct(private readonly User $user) {}

    public function handle(MailerInterface $mailer): void {
        $mailer->to($this->user)->send(new EmailBienvenue($this->user));
    }

    public function failed(\Throwable $e): void {
        Log::error("Échec {$this->user->id}: {$e->getMessage()}");
    }

    // Middleware sur le job
    public function middleware(): array {
        return [new WithoutOverlapping($this->user->id)];
    }
}

EnvoyerEmailBienvenue::dispatch($user);
EnvoyerEmailBienvenue::dispatch($user)->onQueue('emails')->delay(now()->addMinutes(5));
EnvoyerEmailBienvenue::dispatchAfterResponse($user); // après envoi de la réponse HTTP
```

---

**Q97. Comment optimiser les performances en production ?**

```bash
php artisan optimize
composer install --no-dev --optimize-autoloader
```

```env
APP_ENV=production
APP_DEBUG=false
CACHE_DRIVER=redis
QUEUE_CONNECTION=redis
SESSION_DRIVER=redis
```

Côté code :
- `->select()` pour limiter les colonnes
- Eager loading systématique
- LazyCollection pour les gros volumes
- HTTP/2 + gzip côté Nginx
- CDN pour les assets statiques
- OPcache activé
- Réplication MySQL (master/slave)

---

**Q98. Comment utiliser les Lazy Collections dans Laravel ?**

```php
// LazyCollection — streame les données sans les charger toutes en RAM
$résultats = Article::query()->lazy()->filter(fn($a) => $a->estPublié())
                             ->map(fn($a) => $a->titre)
                             ->take(100);

// Avec chunk lazy
Article::lazy(500)->each(function(Article $article) {
    ProcessArticle::dispatch($article);
});

// Collection paresseuse infinie
$nombres = LazyCollection::make(function() {
    $i = 1;
    while (true) yield $i++;
});

$résultat = $nombres->filter(fn($n) => $n % 2 === 0)->take(5)->all();
// [2, 4, 6, 8, 10] — calculé à la demande
```

---

**Q99. Comment profiler et détecter les goulots d'étranglement ?**

```bash
# Laravel Telescope (dev)
composer require laravel/telescope --dev
php artisan telescope:install

# Laravel Debugbar (dev)
composer require barryvdh/laravel-debugbar --dev

# Blackfire, Tideways (production)
```

```php
// Benchmark manuel
$début = microtime(true);
// ... code ...
$durée = (microtime(true) - $début) * 1000;
Log::info("Durée: {$durée}ms");

// Query count
DB::enableQueryLog();
// ...
$requêtes = DB::getQueryLog();
Log::info('Nombre de requêtes : ' . count($requêtes));
```

---

## 16 — Bonnes Pratiques & Patterns

**Q100. Qu'est-ce qu'un Repository Pattern dans Laravel ?**

```php
interface ArticleRepositoryInterface {
    public function findAll(): Collection;
    public function findById(int $id): ?Article;
    public function store(array $data): Article;
    public function update(Article $article, array $data): Article;
    public function delete(Article $article): bool;
}

class EloquentArticleRepository implements ArticleRepositoryInterface {
    public function findAll(): Collection {
        return Article::with('auteur')->latest()->get();
    }
    // ...
}

// Quand l'utiliser : source de données interchangeable,
// logique de requête complexe et réutilisée, grandes équipes.
// Sinon : Eloquent est déjà une abstraction suffisante.
```

---

**Q101. Expliquez le pattern Service Layer dans Laravel.**

```php
class ArticleService {
    public function __construct(
        private readonly ArticleRepository $repository,
        private readonly ImageService      $imageService,
        private readonly TagService        $tagService,
    ) {}

    public function publier(array $données, User $auteur): Article {
        return DB::transaction(function () use ($données, $auteur) {
            $article = $this->repository->create([
                ...$données,
                'auteur_id' => $auteur->id,
                'statut'    => 'publié',
                'publié_le' => now(),
            ]);

            $this->imageService->attacherÀArticle($données['image'] ?? null, $article);
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

**Q102. Qu'est-ce qu'un Action Class ?**
Un **Action** représente une seule opération métier (Single Responsibility maximale), tandis qu'un Service peut regrouper plusieurs opérations liées.

```php
// Action : une seule responsabilité métier (SRP maximal)
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

**Q103. Comment gérer les variables d'environnement correctement ?**

```php
// .env — jamais commité
DB_HOST=127.0.0.1
STRIPE_KEY=sk_live_...

// config/services.php — couche d'abstraction
'stripe' => [
    'key' => env('STRIPE_KEY'),
],

// ✅ Dans le code
$clé = config('services.stripe.key');

// ❌ Jamais hors des fichiers config/
$clé = env('STRIPE_KEY'); // ne fonctionne pas après config:cache
```

---

## 17 — Laravel Avancé

**Q104. Qu'est-ce que les Events & Listeners dans Laravel ?**

```php
// Événement
class ArticlePublié {
    public function __construct(public readonly Article $article) {}
}

// Listener synchrone
class NotifierAbonnés {
    public function handle(ArticlePublié $event): void {
        $event->article->categorie->abonnés->each(function (User $abonné) use ($event) {
            $abonné->notify(new NouvelArticle($event->article));
        });
    }
}

// Listener asynchrone (queue)
class IndexerDansElasticsearch implements ShouldQueue {
    public string $queue = 'search';

    public function handle(ArticlePublié $event): void {
        ElasticsearchService::index($event->article);
    }
}

// Enregistrement (EventServiceProvider ou découverte automatique)
protected $listen = [
    ArticlePublié::class => [
        NotifierAbonnés::class,
        IndexerDansElasticsearch::class,
        EnvoyerTweet::class,
    ],
];

// Dispatch
event(new ArticlePublié($article));
ArticlePublié::dispatch($article); // identique
```

---

**Q105. Qu'est-ce que les Notifications Laravel et leurs canaux ?**

```php
// php artisan make:notification FactureDisponible

class FactureDisponible extends Notification implements ShouldQueue {
    public function __construct(private Facture $facture) {}

    public function via(object $notifiable): array {
        return $notifiable->prefèreEmail() ? ['mail'] : ['mail', 'database'];
    }

    public function toMail(object $notifiable): MailMessage {
        return (new MailMessage)
            ->subject("Facture #{$this->facture->numéro} disponible")
            ->greeting("Bonjour {$notifiable->prenom},")
            ->line("Votre facture de {$this->facture->total}€ est disponible.")
            ->action('Télécharger', route('factures.show', $this->facture))
            ->line('Merci de votre confiance.');
    }

    public function toArray(object $notifiable): array {
        return [
            'facture_id' => $this->facture->id,
            'total'      => $this->facture->total,
            'url'        => route('factures.show', $this->facture),
        ];
    }

    // Canal Slack
    public function toSlack(object $notifiable): SlackMessage {
        return (new SlackMessage)->content("Facture #{$this->facture->numéro} générée.");
    }
}

// Envoi
$user->notify(new FactureDisponible($facture));
Notification::send($users, new FactureDisponible($facture)); // bulk
```

---

**Q106. Comment implémenter le broadcasting temps réel avec Laravel Reverb ?**

```bash
composer require laravel/reverb
php artisan reverb:install
php artisan reverb:start
```

```php
// Événement broadcasté
class MessageEnvoyé implements ShouldBroadcastNow {
    public function __construct(public Message $message) {}

    public function broadcastOn(): array {
        return [new PrivateChannel("chat.{$this->message->conversation_id}")];
    }

    public function broadcastAs(): string {
        return 'message.envoyé';
    }
}

// Autorisation du canal privé
// routes/channels.php
Broadcast::channel('chat.{conversationId}', function (User $user, int $conversationId) {
    return Conversation::find($conversationId)?->membres->contains($user);
});
```

```javascript
// Frontend (Laravel Echo + Reverb)
import Echo from 'laravel-echo'
import Pusher from 'pusher-js'

window.Echo = new Echo({
    broadcaster: 'reverb',
    key:         import.meta.env.VITE_REVERB_APP_KEY,
    wsHost:      import.meta.env.VITE_REVERB_HOST,
    wsPort:      import.meta.env.VITE_REVERB_PORT,
    forceTLS:    false,
})

Echo.private(`chat.${conversationId}`)
    .listen('.message.envoyé', (e) => {
        messages.value.push(e.message)
    })
```

---

**Q107. Comment créer un package Laravel ?**

```
mon-package/
├── src/
│   ├── MonPackageServiceProvider.php
│   ├── Facades/MonPackage.php
│   ├── Commands/MaCommande.php
│   └── Models/MonModel.php
├── config/mon-package.php
├── database/migrations/
├── resources/views/
├── tests/
└── composer.json
```

```php
class MonPackageServiceProvider extends ServiceProvider {
    public function register(): void {
        $this->mergeConfigFrom(__DIR__.'/../config/mon-package.php', 'mon-package');
        $this->app->singleton(MonService::class);
    }

    public function boot(): void {
        $this->loadMigrationsFrom(__DIR__.'/../database/migrations');
        $this->loadViewsFrom(__DIR__.'/../resources/views', 'mon-package');
        $this->loadRoutesFrom(__DIR__.'/../routes/web.php');

        if ($this->app->runningInConsole()) {
            $this->commands([MaCommande::class]);
            $this->publishes([
                __DIR__.'/../config/mon-package.php' => config_path('mon-package.php'),
            ], 'mon-package-config');
        }
    }
}
```

---

**Q108. Qu'est-ce que les Contracts Laravel ?**

Les Contracts sont des interfaces qui définissent les services principaux de Laravel (dans `Illuminate\Contracts`). Ils permettent le couplage lâche.

```php
use Illuminate\Contracts\Cache\Repository as Cache;
use Illuminate\Contracts\Mail\Mailer;
use Illuminate\Contracts\Queue\Queue;
use Illuminate\Contracts\Auth\Guard;

class ArticleService {
    public function __construct(
        private Cache  $cache,  // découplé de l'implémentation Redis/File
        private Mailer $mailer, // découplé de SMTP/Mailgun
    ) {}
}
```

---

**Q109. Comment utiliser les Form Requests avec des règles personnalisées ?**

```php
// Règle personnalisée (classe)
class TitreUnique implements ValidationRule {
    public function validate(string $attribute, mixed $value, Closure $fail): void {
        if (Article::where('titre', $value)->exists()) {
            $fail("Le titre ':attribute' est déjà utilisé.");
        }
    }
}

// Règle inline avec Rule::
$request->validate([
    'email'  => ['required', Rule::unique('users')->ignore($user->id)],
    'statut' => ['required', Rule::in(['brouillon', 'publié', 'archivé'])],
    'tags'   => [Rule::forEach(fn($value) => ['string', 'min:2', 'max:20'])],
]);

// Règle avec After/Before
'date_fin' => ['after:date_debut'],
'code'     => [new TitreUnique()],

// Validation conditionnelle
'carte_numero' => [Rule::requiredIf($request->paiement === 'carte')],
'raison'       => ['exclude_unless:statut,archivé', 'required', 'string'],
```

---

**Q110. Comment fonctionne le scheduling (planificateur de tâches) Laravel ?**

```php
// routes/console.php (Laravel 11) ou app/Console/Kernel.php
Schedule::command('rapport:hebdo')->weeklyOn(1, '08:00');
Schedule::job(new SauvegarderBDD())->dailyAt('03:00');
Schedule::call(fn() => Cache::flush())->hourly();

Schedule::command('import:données')
         ->everyFifteenMinutes()
         ->withoutOverlapping()          // ne pas lancer si déjà en cours
         ->runInBackground()            // ne pas bloquer le scheduler
         ->onOneServer()                // une seule instance sur cluster
         ->emailOutputOnFailure('admin@app.com')
         ->before(fn() => Log::info('Début import'))
         ->after(fn() => Log::info('Fin import'));

// Cron server (une seule entrée crontab)
// * * * * * php /var/www/artisan schedule:run >> /dev/null 2>&1
```

---

**Q111. Qu'est-ce que Laravel Horizon et quand l'utiliser ?**

```bash
composer require laravel/horizon
php artisan horizon:install
php artisan horizon
```

Horizon est un tableau de bord et un superviseur de queues Redis. Il fournit :
- Supervision des workers en temps réel
- Métriques (throughput, temps de traitement, échecs)
- Alertes et notifications
- Balance automatique des workers

```php
// config/horizon.php
'environments' => [
    'production' => [
        'supervisor-1' => [
            'connection'    => 'redis',
            'queue'         => ['high', 'default', 'emails'],
            'balance'       => 'auto',
            'autoScalingStrategy' => 'time',
            'minProcesses'  => 1,
            'maxProcesses'  => 20,
            'tries'         => 3,
            'timeout'       => 60,
        ],
    ],
],
```

---

**Q112. Comment implémenter la multi-tenancy dans Laravel ?**

```php
// Approche 1 : Base de données par tenant (isolation maximale)
class TenantManager {
    public function configurer(Tenant $tenant): void {
        config([
            'database.connections.tenant' => [
                'driver'   => 'mysql',
                'database' => "tenant_{$tenant->slug}",
                // ...
            ],
        ]);
        DB::purge('tenant');
        DB::reconnect('tenant');
    }
}

// Approche 2 : Colonne tenant_id (global scope)
class TenantScope implements Scope {
    public function apply(Builder $builder, Model $model): void {
        $builder->where('tenant_id', tenant()->id);
    }
}

// Packages populaires : spatie/laravel-multitenancy, stancl/tenancy
```

---

**Q113. Comment utiliser les Livewire avec Laravel ?**

```php
// php artisan make:livewire RechercheArticles
class RechercheArticles extends Component {
    public string $recherche     = '';
    public string $tri           = 'created_at';
    public int    $parPage       = 10;

    // URL query string synchronisé automatiquement
    #[Url]
    public string $recherche = '';

    public function updatingRecherche(): void {
        $this->resetPage();
    }

    public function render(): View {
        return view('livewire.recherche-articles', [
            'articles' => Article::search($this->recherche)
                                 ->orderBy($this->tri, 'desc')
                                 ->paginate($this->parPage),
        ]);
    }
}
```

```blade
{{-- resources/views/livewire/recherche-articles.blade.php --}}
<div>
    <input wire:model.live.debounce.300ms="recherche" placeholder="Rechercher..." />
    <select wire:model="tri">
        <option value="created_at">Date</option>
        <option value="titre">Titre</option>
    </select>

    @foreach($articles as $article)
        <div>{{ $article->titre }}</div>
    @endforeach

    {{ $articles->links() }}
</div>
```

---

**Q114. Comment utiliser les Spatie packages courants dans Laravel ?**

```bash
# Permissions et rôles
composer require spatie/laravel-permission

# Médias
composer require spatie/laravel-medialibrary

# Activité / Audit log
composer require spatie/laravel-activitylog

# Query builder
composer require spatie/laravel-query-builder
```

```php
// Permissions
$user->assignRole('admin');
$user->givePermissionTo('modifier-article');
$user->hasPermissionTo('modifier-article');
Gate::before(fn($user, $ability) => $user->hasRole('super-admin') ? true : null);

// MediaLibrary
class Article extends Model {
    use HasMedia;
    public function registerMediaCollections(): void {
        $this->addMediaCollection('images')->useDisk('s3');
        $this->addMediaCollection('thumbnail')->singleFile();
    }
}
$article->addMedia($fichier)->toMediaCollection('images');
$article->getFirstMediaUrl('thumbnail', 'thumb-300');

// QueryBuilder (filtres, sorts, includes via URL)
$articles = QueryBuilder::for(Article::class)
    ->allowedFilters(['titre', 'statut', AllowedFilter::scope('publié')])
    ->allowedSorts(['titre', 'created_at', 'views_count'])
    ->allowedIncludes(['auteur', 'tags', 'commentaires'])
    ->paginate();
```

---

## 18 — Architecture & Scalabilité

**Q115. Qu'est-ce que l'architecture hexagonale (Ports & Adapters) ?**

```
Domain (métier pur, sans dépendances framework)
├── Entities/         Article, Commande, User
├── ValueObjects/     Argent, Email, Slug
├── Repositories/     ArticleRepository (interface)
└── Services/         PublierArticleService (logique métier)

Application (orchestration)
├── Commands/         PublierArticleCommand
├── Handlers/         PublierArticleHandler
└── DTOs/             ArticleDTO

Infrastructure (implémentations concrètes)
├── Persistence/      EloquentArticleRepository
├── Mail/             MailgunMailer
└── Cache/            RedisArticleCache

Presentation (adapters)
├── Http/             ArticleController
├── Console/          PublierArticleCommand
└── Queue/            PublierArticleJob
```

---

**Q116. Qu'est-ce que CQRS (Command Query Responsibility Segregation) ?**

```php
// Command (écrit, change l'état)
class PublierArticleCommand {
    public function __construct(
        public readonly int    $articleId,
        public readonly int    $auteurId,
        public readonly string $titre,
    ) {}
}

class PublierArticleHandler {
    public function handle(PublierArticleCommand $command): void {
        $article = Article::findOrFail($command->articleId);
        $article->publier();
        event(new ArticlePublié($article));
    }
}

// Query (lit, ne change rien)
class ArticlesAccueilQuery {
    public function execute(int $limit = 10): Collection {
        return Article::select('id', 'titre', 'slug', 'publié_le')
                      ->where('statut', 'publié')
                      ->with('auteur:id,nom')
                      ->latest('publié_le')
                      ->limit($limit)
                      ->get();
    }
}

// Bus (Laravel)
app(CommandBus::class)->dispatch(new PublierArticleCommand(...));
```

---

**Q117. Comment concevoir une application Laravel scalable ?**

**Séparation des responsabilités :**
- Contrôleurs minces → Services → Repositories
- Jobs asynchrones pour tout ce qui n'est pas critique au chemin de réponse
- Events pour le découplage des modules

**Infrastructure :**
- Redis pour cache, sessions, queues
- CDN pour assets statiques
- Réplication MySQL (1 master N slaves) + connexions read/write séparées
- Sharding horizontal via MongoDB ou partition MySQL

**Monitoring :**
- Laravel Telescope (dev), Horizon (queues)
- Sentry pour les erreurs
- New Relic / Datadog pour les métriques APM

```php
// Connexion en lecture séparée
'mysql' => [
    'read'  => ['host' => ['192.168.1.1', '192.168.1.2']], // slaves
    'write' => ['host' => ['192.168.1.3']],                 // master
    'sticky'=> true,
    // ...
],
```

---

**Q118. Qu'est-ce que les microservices et Laravel dans ce contexte ?**

Laravel peut jouer plusieurs rôles dans une architecture microservices :
- **Service autonome** : API REST avec Sanctum/Passport
- **Orchestrateur** : appelle d'autres services via `Http::` (Laravel HTTP Client)
- **Consommateur d'événements** : via RabbitMQ, Kafka (package enqueue/laravel-queue)

```php
// HTTP Client (wraps Guzzle)
$réponse = Http::withToken($token)
               ->retry(3, 100)
               ->timeout(5)
               ->post('https://service-paiement/charge', [
                   'montant' => $commande->total,
                   'devise'  => 'EUR',
               ]);

if ($réponse->failed()) {
    throw new PaiementException($réponse->json('message'));
}
```

---

## 19 — DevOps & Déploiement

**Q119. Comment déployer une application Laravel en production ?**

```bash
# Checklist de déploiement
git pull origin main
composer install --no-dev --optimize-autoloader
php artisan migrate --force
php artisan optimize              # config + routes + views + events
php artisan queue:restart         # redémarrer les workers queue
php artisan horizon:terminate     # si Horizon

# Variables d'environnement production
APP_ENV=production
APP_DEBUG=false
APP_KEY=base64:...
DB_*=...
REDIS_*=...
MAIL_*=...
```

---

**Q120. Comment configurer un pipeline CI/CD pour Laravel ?**

```yaml
# .github/workflows/laravel.yml
name: Laravel CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_DATABASE: test_db
          MYSQL_ROOT_PASSWORD: secret
        ports: ['3306:3306']
      redis:
        image: redis
        ports: ['6379:6379']

    steps:
      - uses: actions/checkout@v4

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.3'
          extensions: mbstring, pdo, redis

      - name: Install dependencies
        run: composer install --no-interaction --prefer-dist

      - name: Copy env
        run: cp .env.example .env && php artisan key:generate

      - name: Run migrations
        run: php artisan migrate

      - name: Run tests
        run: php artisan test --parallel

      - name: Static analysis (PHPStan)
        run: vendor/bin/phpstan analyse

      - name: Code style (Pint)
        run: vendor/bin/pint --test
```

---

**Q121. Comment utiliser Docker avec Laravel ?**

```dockerfile
# Dockerfile
FROM php:8.3-fpm-alpine

RUN apk add --no-cache \
    libpng-dev libjpeg-dev freetype-dev libzip-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install pdo_mysql gd zip opcache pcntl

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
WORKDIR /var/www
COPY . .
RUN composer install --no-dev --optimize-autoloader \
    && php artisan optimize
```

```yaml
# docker-compose.yml
services:
  app:
    build: .
    volumes: ['.:/var/www']

  nginx:
    image: nginx:alpine
    ports: ['80:80']
    volumes: ['./nginx.conf:/etc/nginx/conf.d/default.conf']

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_DATABASE: ${DB_DATABASE}
      MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}

  redis:
    image: redis:alpine

  queue:
    build: .
    command: php artisan queue:work --tries=3
    depends_on: [app, redis]

  horizon:
    build: .
    command: php artisan horizon
    depends_on: [app, redis]
```

---

## 20 — Questions Situationnelles & Conception

**Q122. Comment concevriez-vous un système de commentaires imbriqués ?**

```php
// Modèle avec closure table ou nested set
class Commentaire extends Model {
    public function parent(): BelongsTo {
        return $this->belongsTo(self::class, 'parent_id');
    }

    public function enfants(): HasMany {
        return $this->hasMany(self::class, 'parent_id');
    }

    // Récursif (attention aux N+1)
    public function enfantsRécursifs(): HasMany {
        return $this->enfants()->with('enfantsRécursifs');
    }
}

// Migration
Schema::create('commentaires', function (Blueprint $table) {
    $table->id();
    $table->foreignId('article_id')->constrained()->cascadeOnDelete();
    $table->foreignId('user_id')->constrained();
    $table->foreignId('parent_id')->nullable()->constrained('commentaires');
    $table->integer('profondeur')->default(0);
    $table->text('contenu');
    $table->timestamps();
    $table->index(['article_id', 'parent_id']);
});

// Requête optimisée
$commentaires = Commentaire::where('article_id', $articleId)
    ->whereNull('parent_id')
    ->with('enfantsRécursifs.auteur')
    ->latest()
    ->get();
```

---

**Q123. Comment concevriez-vous un système de recherche avec filtres avancés ?**

```php
class ArticleFiltre {
    public function __construct(private Builder $query) {}

    public static function appliquer(Builder $query): self {
        return (new self($query))
            ->filtrerParRecherche(request('q'))
            ->filtrerParCategorie(request('categorie'))
            ->filtrerParTags(request('tags'))
            ->filtrerParDate(request('depuis'), request('jusque'))
            ->trier(request('tri', 'created_at'), request('ordre', 'desc'));
    }

    private function filtrerParRecherche(?string $terme): self {
        if ($terme) {
            $this->query->where(function($q) use ($terme) {
                $q->where('titre', 'LIKE', "%{$terme}%")
                  ->orWhere('contenu', 'LIKE', "%{$terme}%");
                  // Ou : ->whereFullText(['titre', 'contenu'], $terme)
            });
        }
        return $this;
    }

    private function filtrerParTags(?array $tags): self {
        if ($tags) {
            $this->query->whereHas('tags', fn($q) => $q->whereIn('nom', $tags));
        }
        return $this;
    }

    public function get(): Collection {
        return $this->query->get();
    }
}

// Utilisation
$articles = ArticleFiltre::appliquer(Article::query())->get();
```

---

**Q124. Comment implémenteriez-vous un système de paiement robuste ?**

```php
interface PaiementGatewayInterface {
    public function créerIntention(float $montant, string $devise): IntentionPaiement;
    public function confirmer(string $intentionId): RésultatPaiement;
    public function rembourser(string $chargeId, float $montant): RésultatRemboursement;
}

class StripeGateway implements PaiementGatewayInterface {
    public function créerIntention(float $montant, string $devise): IntentionPaiement {
        $intention = $this->stripe->paymentIntents->create([
            'amount'   => (int)($montant * 100),
            'currency' => strtolower($devise),
        ]);
        return new IntentionPaiement($intention->id, $intention->client_secret);
    }
    // ...
}

class PaiementService {
    public function traiterCommande(Commande $commande): void {
        DB::transaction(function () use ($commande) {
            $intention = $this->gateway->créerIntention($commande->total, 'EUR');

            Paiement::create([
                'commande_id'  => $commande->id,
                'intention_id' => $intention->id,
                'statut'       => 'en_attente',
                'montant'      => $commande->total,
            ]);

            $commande->update(['statut' => 'paiement_en_cours']);
        });
    }
}
```

---

**Q125. Comment débogueriez-vous une fuite mémoire dans une application Laravel ?**

```php
// 1. Identifier avec memory_get_peak_usage
Log::info('Mémoire pic: ' . memory_get_peak_usage(true) / 1024 / 1024 . 'MB');

// 2. Suspects courants
// a) Modèles chargés en masse
Article::all(); // ❌ si millions d'articles
Article::lazy()->each(fn($a) => traiter($a)); // ✅

// b) Query log actif en production
DB::disableQueryLog(); // désactiver en prod

// c) Événements cumulés
// Vérifier les observers et listeners qui retiennent des références

// d) Closures dans des attributs statiques
// Nettoyer les références circulaires

// 3. Outils
// php-meminfo, Blackfire, Valgrind
// php artisan queue:work --memory=128 → arrêt automatique si dépassé

// 4. Worker queue : redémarrer périodiquement
// Supervisor : autorestart=true
// Horizon : --memory=256
```

---

## 🔗 Ressources Complémentaires

- [Documentation officielle Laravel](https://laravel.com/docs)
- [PHP The Right Way](https://phptherightway.com)
- [Laracasts](https://laracasts.com)
- [Laravel News](https://laravel-news.com)
- [Spatie Guidelines Laravel](https://spatie.be/guidelines/laravel-php)
- [MongoDB Laravel Driver](https://www.mongodb.com/docs/drivers/php/laravel-mongodb/)
- [Redis Documentation](https://redis.io/docs/)
- [Inertia.js Documentation](https://inertiajs.com)
- [PHPStan — Static Analysis](https://phpstan.org)
- [Laravel Pint — Code Style](https://laravel.com/docs/pint)
- [PHP 8 RFC](https://wiki.php.net/rfc)

---

## 📊 Résumé des Topics par Niveau

| Niveau | Topics recommandés |
|---|---|
| 🟢 Junior | PHP fondamentaux (Q1–Q14), POO bases (Q15–Q18), Laravel MVC (Q23–Q29), Eloquent bases (Q35–Q40), Tests basiques (Q54–Q57) |
| 🟡 Intermédiaire | Eloquent avancé (Q41–Q43), API REST (Q47–Q51), Redis bases (Q73–Q76), Inertia.js (Q80–Q88), Sécurité (Q89–Q94), Queues (Q96) |
| 🔴 Senior | MongoDB avancé (Q68–Q72), Redis avancé (Q77–Q79), Laravel avancé (Q104–Q114), Architecture (Q115–Q118), DevOps (Q119–Q121), Conception (Q122–Q125) |

---

## 📄 Licence

Ce document est libre d'utilisation à des fins éducatives et de préparation aux entretiens.

---

*Maintenu avec ❤️ pour la communauté PHP/Laravel francophone — 125 questions couvrant tout le spectre Junior → Senior*