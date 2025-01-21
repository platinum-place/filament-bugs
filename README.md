# filament-bugs

## Problemas con la carga de archivos

Si encuentras errores al usar acciones para cargar archivos en Livewire, esto puede estar relacionado con la pre-carga de archivos en la página.

### Solución
Asegúrate de ajustar el middleware `TrustProxies`. Esto ayuda a manejar correctamente las solicitudes relacionadas con archivos en tu aplicación.

Consulta la documentación oficial de Laravel sobre [TrustProxies](https://laravel.com/docs/10.x/requests#configuring-trusted-proxies) para más detalles.

#### Laravel 11 o superiores (bootstrap/app.php):
```php
return Application::configure(basePath: dirname(__DIR__))
    ->withMiddleware(function (Middleware $middleware) {
        $middleware->trustProxies(at: '*');
    })
```

#### Laravel 10 o inferiores (app/Http/Middleware/TrustProxies.php):

```php
protected $proxies = '*';
```

## Problemas con el Logout en Filament

Cuando utilizas Filament junto con otros sistemas de autenticación, como Breeze o Passport, el logout puede fallar debido a que Filament no encuentra la ruta de login para redirigir después de cerrar sesión.

### Causa
Esto sucede porque Filament, por defecto, intenta redirigir a `/login` después del logout. Si esta ruta no está definida, el sistema genera un error.

### Solución
Es necesario registrar una ruta global `/login` para que Filament siempre tenga una referencia. Esta ruta debe redirigir al login del panel configurado como predeterminado.

Para más detalles, revisa la documentación oficial de [Filament](https://filamentphp.com/docs) sobre la configuración de rutas y autenticación.

Dentro de routes/web.php:
```php
Route:get('/login',function(){
return redirect()->route('filament.admin.auth.login');
})->name('login');
```

## Carga de Dependencias

Aunque la documentación de Filament no exige usar dependencias de Node si no son necesarias, muchos componentes de Filament dependen de ellas para funcionar correctamente, incluso si otros no las utilizan.

### Recomendación
Es recomendable tener configurado Node y ejecutar `npm run build` para garantizar que el CSS y el JS se carguen correctamente, especialmente en entornos de producción.

#### Producción con HTTPS (app/Providers/AppServiceProvider.php, public function boot())
En entornos de producción con HTTPS, asegúrate de forzar el esquema seguro:
```php
if ($this->app->environment('production')) {
    URL::forceScheme('https');
}
```

