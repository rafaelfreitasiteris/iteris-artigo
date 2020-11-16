*Public Alvo*: Fernando [x]
*Categoria do Texto*: Mobile

*Palavra Chave (Head Tail)*: processo de build
*Palavra Chave (Head Tail)*: processo de build em aplicativos IOS

*Palavras Chave*: processo de compilação IOS, Xcode compiler, swiftc compiler, dynamic framework, static framework

*Topicos Abordados*: swiftc compiler, estrutura base dos aplicativos, info.plist, flags do compilador, instalação de teste

# Titulo: Compile seu app IOS sem o Xcode.
# Resumo
Um conhecimento profundo de como funciona o processo de build de uma aplicação IOS, pode fazer você entender muitos enigmas da plataforma. E elevará sua independência do *XCode*. 

# Texto
### Introdução

Quem nunca passou pela situação de estar trabalhando em uma aplicação IOS e receber um erro absolutamente sem sentido vindo da IDE? Ou até mesmo um comportamento inesperado da aplicação já em tempo de execução? 

O primeiro caminho é sempre a busca por uma solução mirabolante no *stackoverflow* ou qualquer forum de desenvolvimento. Solução que você usa e não faz a menor idéia do por que funciona ou até do por quê não funciona. Isso geralmente ocorre devido uma falta de conhecimento de como as coisas funcionam por detrás da pomposa interface do *XCode*. 

### Desenvolvimento

E a partir daqui me permita lhe frustrar um pouco: por mais que pareça excitante a idéia de não usar o *XCode* pra gerar uma aplicação IOS isso não é possível. Por que mesmo sem abrir-lo, alguns executáveis vindos de seu toolbox serão necessários como por exemplo: o *swiftc*.

Vamos testar se eles esta funcionando?

```
echo “print(2 + 2)” > SwiftFile.swift
swiftc SwiftFile.swift
./SwiftFile
```

Nesse três comandos: 

* criamos um arquivo *swift* chamado `SwiftFile.swift` com o conteúdo de um print de uma soma `(2 + 2)`.
* o compilador do swift gera um executável baseado no arquivo `SwiftFile.swift`
* O executável é acionado e apresenta o resultado esperado da soma: *4*

Com isso já podemos gerar o nosso aplicativo IOS:

*1 passo*: criar uma pasta com o nome do seu aplicativo e extensão `.app`.

`mkdir IterisApp.app`

Como pode ser visto o nome do meu aplicativo será *IterisApp* :)

*2 passo*: vamos criar o nosso arquivo `Info.plist`

```
<?xml version=“1.0” encoding=“UTF-8”?>
<!DOCTYPE plist PUBLIC “-//Apple//DTD PLIST 1.0//EN” “http://www.apple.com/DTDs/PropertyList-1.0.dtd”>
<plist version=“1.0”>
<dict>
	<key>CFBundleDevelopmentRegion</key>
	<string>en</string>
	<key>CFBundleExecutable</key>
	<string>IterisApp</string>
	<key>CFBundleIdentifier</key>
	<string>br.com.iteris.app</string>
	<key>CFBundleInfoDictionaryVersion</key>
	<string>6.0</string>
	<key>CFBundleName</key>
	<string>IterisApp</string>
	<key>CFBundlePackageType</key>
	<string>APPL</string>
	<key>CFBundleShortVersionString</key>
	<string>1.0</string>
	<key>CFBundleVersion</key>
	<string>1</string>
	<key>LSRequiresIPhoneOS</key>
	<true/>
	<key>UIRequiredDeviceCapabilities</key>
	<array>
		<string>armv7</string>
	</array>
	<key>UISupportedInterfaceOrientations</key>
	<array>
		<string>UIInterfaceOrientationPortrait</string>
		<string>UIInterfaceOrientationLandscapeLeft</string>
		<string>UIInterfaceOrientationLandscapeRight</string>
	</array>
	<key>UISupportedInterfaceOrientations~iPad</key>
	<array>
		<string>UIInterfaceOrientationPortrait</string>
		<string>UIInterfaceOrientationPortraitUpsideDown</string>
		<string>UIInterfaceOrientationLandscapeLeft</string>
		<string>UIInterfaceOrientationLandscapeRight</string>
	</array>
</dict>
</plist>
```

Algumas informações a respeito desse arquivos:

`CFBundleExecutable: IterisApp` o nome do executável.
`CFBundleIdentifier: br.com.iteris.app` o identificador único da aplicação
`CFBundleExecutable: APPL` o que sinaliza que estamos criar uma aplicação mobile.

Esse arquivo deve ser colocado dentro da pasta `IterisApp.app`.

*3 passo*: A criação de dois arquivos essenciais:

O arquivo `main.swift` 

```
import UIKit

UIApplicationMain(
    CommandLine.argc,
    CommandLine.unsafeArgv,
    nil,
    NSStringFromClass(AppDelegate.self)
)
```

O arquivo  `AppDelegate.swift`

```
import UIKit

class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        self.window = UIWindow(frame: UIScreen.main.bounds)
        
        let vc = UIViewController()
        vc.view.backgroundColor = .white

        self.window?.rootViewController = vc
        self.window?.makeKeyAndVisible()

        let label = UILabel()
        label.translatesAutoresizingMaskIntoConstraints = false
        label.text = "Iteris Anywhere and Together"

        vc.view.addSubview(label)

        label.centerXAnchor.constraint(equalTo: vc.view.centerXAnchor).isActive = true
        label.centerYAnchor.constraint(equalTo: vc.view.centerYAnchor).isActive = true

        return true
    }
}
```

Como pode ser visto aqui na classe `AppDelegate`, estamos criando uma tela com o texto `Iteris Anywhere and Together` centralizado em um fundo branco.

*4 passo*: compilar os arquivos  `AppDelegate` e `main.swift`

```
SDK = /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator14.0.sdk

ARCH_TARGET = x86_64-apple-ios14.0-simulator

swiftc main.swift AppDelegate.swift \
-target $(ARCH_TARGET) -sdk $(SDK) \
-o IterisApp
```

Perceba a criação de duas variáveis:  `SDK` e `ARCH_TARGET` 

`SDK`: aponta aonde estar o SDK do IOS e qual a versão desejada.
`ARCH_TARGET `: aponta pra qual arquitetura o executável será compilado

Optei pela utilização da versão 14 do SDK com a arquitetura do simulador do IOS também na versão 14.0.

Após isso utilizamos o compilador swift para compilar os arquivos `main.swift` e `AppDelegate.swift`. 

Foram utilizadas também as flags `-target` e `-sdk` das quais foram alimentadas com os valores das variáveis posteriormente definidas. Por último a flag `-o` na qual se define o novo do executável criado pelo compilador. No caso foi escolhido o nome `IterisApp`.

Se tudo ocorreu conforme esperado, agora você já pode ver o arquivo `IterisApp` disponível.

*5 passo*: mova o executável gerado pelo ultimo comando para a pasta 
`IterisApp.app`

Com isso, se tudo foi feito corretamente, nesse momento a pasta `IterisApp.app` possui dois arquivos: `Info.plist` e `IterisApp`.

Agora abra o simulador com o comando

```
open -a Simulator.app
```

E arraste a pasta `IterisApp.app` pra dentro do simulador. Com isso o aplicativo será instalado e permitirá que vc faça o teste. Assim:

[[GIF AQUI]]

### Conclusão

Com isso a gente percebe que um aplicativo nada mais é do que um executável mais um arquivo `Info.plist` com algumas mínimas informações. 

Porém, você deve saber que aplicações IOS modernas não são tão simples como se fez parecer aqui. Geralmente envolve outros tipos de artefatos como `.frameworks`, `.bundles` e outros.  Esses artefatos podem ser compilados de maneira dinâmica `(.dylib)` ou estática `(.a)` , sabe qual a diferença entre eles? 

Bem, continua acompanhando o blog da Iteris que a gente pode trazer mais informações a respeito desse tema :)
