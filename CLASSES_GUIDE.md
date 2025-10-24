# HScript-Iris Class Support Guide

## 🎉 Nueva Funcionalidad: Soporte de Clases

Esta fork de hscript-iris ahora soporta la definición de clases personalizadas en scripts HScript.

## 📋 Características Implementadas

### ✅ Clases Simples
```haxe
class MiClase {
    public var nombre:String;
    public var edad:Int;
    
    public function new(n:String, e:Int) {
        nombre = n;
        edad = e;
    }
    
    public function saludar() {
        trace("Hola, soy " + nombre + " y tengo " + edad + " años");
    }
}

var persona = new MiClase("Juan", 25);
persona.saludar(); // Output: "Hola, soy Juan y tengo 25 años"
```

### ✅ Herencia (extends)
```haxe
class Animal {
    public var nombre:String;
    
    public function new(n:String) {
        nombre = n;
    }
    
    public function hacerSonido() {
        trace(nombre + " hace un sonido");
    }
}

class Perro extends Animal {
    public function new(n:String) {
        super(n);
    }
    
    public override function hacerSonido() {
        trace(nombre + " dice: ¡Guau!");
    }
}

var miPerro = new Perro("Rex");
miPerro.hacerSonido(); // Output: "Rex dice: ¡Guau!"
```

### ✅ Implementación de Interfaces
```haxe
class MiSprite extends FlxSprite implements IFlxDestroyable {
    public var miVariable:Int = 0;
    
    public function new(x:Float, y:Float) {
        super(x, y);
        makeGraphic(100, 100, FlxColor.RED);
    }
    
    override public function destroy() {
        super.destroy();
        trace("MiSprite destruido");
    }
}

var sprite = new MiSprite(100, 100);
PlayState.instance.add(sprite);
```

### ✅ Modificadores Soportados
- `public` / `private` - Visibilidad de campos y métodos
- `static` - Campos y métodos estáticos
- `final` - Variables constantes
- `override` - Sobrescritura de métodos

### ✅ Campos de Clase
```haxe
class Contador {
    public static var total:Int = 0;
    public var numero:Int;
    public final ID:String;
    
    public function new(id:String) {
        ID = id;
        numero = total++;
    }
    
    public static function reset() {
        total = 0;
    }
}

var c1 = new Contador("A");
var c2 = new Contador("B");
trace(Contador.total); // Output: 2
```

## 🔧 Cómo Habilitar en tu Proyecto

### 1. Actualizar Project.xml
```xml
<haxelib name="hscript-iris" version="git" url="https://github.com/LeninAsto/hscript-iris"/>
```

### 2. Configurar IrisConfig
```haxe
// Habilitar soporte de clases
var config = new IrisConfig("MyScript", true, true, true); // El 4to parámetro es allowClasses
var script = new Iris(scriptCode, config);
```

## 📝 Ejemplos de Uso en FNF Plus Engine

### Ejemplo 1: Clase de Personaje Personalizado
```haxe
class PersonajeCustom extends Character {
    public var habilidadEspecial:String;
    
    public function new(x:Float, y:Float, char:String) {
        super(x, y, char);
        habilidadEspecial = "Cantar bien";
    }
    
    public function usarHabilidad() {
        playAnim('hey', true);
        trace("Usando: " + habilidadEspecial);
    }
}

// Crear instancia
var miPersonaje = new PersonajeCustom(100, 100, 'bf');
PlayState.instance.add(miPersonaje);
```

### Ejemplo 2: Sistema de Partículas
```haxe
class Particula extends FlxSprite {
    public var velocidadX:Float;
    public var velocidadY:Float;
    public var vida:Float;
    
    public function new(x:Float, y:Float) {
        super(x, y);
        makeGraphic(5, 5, FlxColor.WHITE);
        velocidadX = FlxG.random.float(-100, 100);
        velocidadY = FlxG.random.float(-200, -50);
        vida = 1.0;
    }
    
    override public function update(elapsed:Float) {
        super.update(elapsed);
        x += velocidadX * elapsed;
        y += velocidadY * elapsed;
        velocidadY += 500 * elapsed; // gravedad
        vida -= elapsed;
        alpha = vida;
        
        if (vida <= 0) {
            kill();
        }
    }
}

class SistemaParticulas {
    public var particulas:FlxTypedGroup<Particula>;
    
    public function new() {
        particulas = new FlxTypedGroup<Particula>();
        PlayState.instance.add(particulas);
    }
    
    public function emitir(x:Float, y:Float, cantidad:Int) {
        for (i in 0...cantidad) {
            var p = new Particula(x, y);
            particulas.add(p);
        }
    }
}

// Usar el sistema
var sistema = new SistemaParticulas();
sistema.emitir(640, 360, 20);
```

### Ejemplo 3: Manager de Estado
```haxe
class GameManager {
    public static var instance:GameManager;
    public var puntos:Int = 0;
    public var nivel:Int = 1;
    
    public function new() {
        instance = this;
    }
    
    public function agregarPuntos(cantidad:Int) {
        puntos += cantidad;
        if (puntos >= nivel * 1000) {
            subirNivel();
        }
    }
    
    public function subirNivel() {
        nivel++;
        trace("¡Nivel subido! Ahora estás en el nivel " + nivel);
    }
    
    public function reset() {
        puntos = 0;
        nivel = 1;
    }
}

// Inicializar
var manager = new GameManager();
manager.agregarPuntos(500);
```

## ⚠️ Limitaciones Actuales

1. **Super en constructores**: El soporte de `super()` es limitado en la versión actual
2. **Métodos estáticos**: Funcionan pero con algunas limitaciones
3. **Getters/Setters**: No implementados aún
4. **Propiedades abstractas**: No soportadas

## 🐛 Solución de Problemas

### Error: "Cannot access private field locals"
- Asegúrate de que `locals` es público en `Interp.hx`

### Error: "Unmatched patterns: EClass"
- Verifica que agregaste el caso `EClass` en `Printer.hx` y `Tools.hx`

### Las clases no se instancian
- Asegúrate de que `allowClasses = true` en tu `IrisConfig`

## 🚀 Cambios Realizados

### Archivos Modificados:
1. `crowplexus/iris/IrisConfig.hx` - Agregado parámetro `allowClasses`
2. `crowplexus/hscript/Expr.hx` - Agregada expresión `EClass` y tipos relacionados
3. `crowplexus/hscript/Parser.hx` - Agregado parsing de clases
4. `crowplexus/hscript/Interp.hx` - Agregado manejo de clases en runtime
5. `crowplexus/hscript/Printer.hx` - Agregado soporte para imprimir clases
6. `crowplexus/hscript/Tools.hx` - Agregado soporte en funciones auxiliares

## 📚 Recursos Adicionales

- [Documentación Original de HScript](https://github.com/HaxeFoundation/hscript)
- [FNF Psych Engine Wiki](https://github.com/ShadowMario/FNF-PsychEngine/wiki)
- [Plus Engine GitHub](https://github.com/LeninAsto/FNF-PlusEngine)

---

**Creado para FNF Plus Engine por LeninAsto**
