# Dispositivos_Moveis

Projeto inicial de **Desenvolvimento de Sistemas para Dispositivos Móveis**.

| Item | Valor |
| --- | --- |
| Nome do projeto | `Dispositivos_Moveis` |
| Módulo | `:app` |
| Package / `namespace` / `applicationId` | `com.example.dispositivosmoveis` |
| Linguagem | Kotlin + Jetpack Compose |
| Build | Kotlin DSL (`.kts`) + Version Catalog |

Requisito: **Android Studio Ladybug (ou mais recente)** com **JDK 17** e Android SDK **API 35**.

---

## Estrutura

```
Dispositivos_Moveis/
├── settings.gradle.kts              # rootProject.name + include(":app")
├── build.gradle.kts                 # plugins do catálogo (apply false)
├── gradle.properties
├── local.properties                 # GERADO pelo Android Studio (não versionado)
├── local.properties.example
├── gradle/
│   ├── libs.versions.toml           # Version Catalog
│   └── wrapper/gradle-wrapper.properties
└── app/                             # módulo :app
    ├── build.gradle.kts
    └── src/main/java/com/example/dispositivosmoveis/
        └── MainActivity.kt
```

---

## 1. `gradle/libs.versions.toml`

Catálogo de versões com as dependências essenciais do Android moderno (AndroidX KTX, Lifecycle, Activity Compose, Compose BOM / Material 3 e testes).

As aliases do catálogo viram referências `libs.*` nos arquivos `.kts`.

---

## 2. `app/build.gradle.kts` (Module `:app`)

Pontos-chave já configurados:

- `namespace = "com.example.dispositivosmoveis"`
- `applicationId = "com.example.dispositivosmoveis"`
- plugins e dependências via Version Catalog (`alias(libs.plugins.*)` e `implementation(libs.*)`)
- Jetpack Compose habilitado (`buildFeatures { compose = true }`)

---

## 3. `MainActivity`

Pacote: `com.example.dispositivosmoveis`

Arquivo:

`app/src/main/java/com/example/dispositivosmoveis/MainActivity.kt`

É uma `ComponentActivity` com tela Compose. Se o emulador mostrar a mensagem de sucesso, o **Run** e o **Running Devices** estão ok.

---

## 4. Passo a passo: Running Devices + botão Run (Play)

O botão verde **Run** só fica habilitado quando **três** coisas estão certas ao mesmo tempo:

1. O Gradle terminou o **Sync** sem erro.
2. O `local.properties` aponta para um SDK válido.
3. Existe um **dispositivo/emulador** selecionado na barra de ferramentas.

### 4.1 Abrir o projeto

1. Abra o **Android Studio**.
2. **File → Open…** e selecione a pasta raiz `Dispositivos_Moveis` (a que contém `settings.gradle.kts`).
3. Confirme **Trust Project** se aparecer o aviso.
4. Em **Settings → Build, Execution, Deployment → Build Tools → Gradle**, defina **Gradle JDK** como **17**.
5. Se a IDE avisar que o `gradle-wrapper.jar` está ausente, aceite **Generate Gradle Wrapper** (ou rode `gradle wrapper --gradle-version 8.9` com o Gradle do sistema). O `gradle-wrapper.properties` já pede o Gradle **8.9**, compatível com o AGP 8.7.3.
6. Espere o **Gradle Sync** (barra inferior). Deve terminar com *BUILD SUCCESSFUL*.

Se o Sync falhar com *SDK location not found*, vá direto para a seção 4.3.

### 4.2 Embutir o emulador na aba lateral (Running Devices)

No Android Studio atual o emulador já nasce **dentro** da IDE. Confirme a opção:

**Windows / Linux**

1. **File → Settings…** (ou `Ctrl+Alt+S`).
2. **Tools → Emulator**.
3. Marque **Launch in the Running Devices tool window**.
4. **Apply → OK**.

**macOS**

1. **Android Studio → Settings…** (ou `⌘,`).
2. **Tools → Emulator**.
3. Marque **Launch in the Running Devices tool window**.
4. **OK**.

Abra a aba:

- **View → Tool Windows → Running Devices**

Ela costuma ficar na coluna da **direita**, junto de *App Inspection* / *Device Manager*.

> Para abrir o emulador em janela separada, **desmarque** essa mesma opção.

### 4.3 Verificar se o `local.properties` está lendo o SDK

O arquivo **não vai para o Git** (está no `.gitignore`). O Android Studio cria ele na raiz na primeira abertura.

1. Na raiz do projeto, abra `local.properties`.
2. Precisa existir **exatamente** uma linha `sdk.dir=...` apontando para o Android SDK:

```properties
# Windows
sdk.dir=C\:\\Users\\SEU_USUARIO\\AppData\\Local\\Android\\Sdk

# macOS
sdk.dir=/Users/SEU_USUARIO/Library/Android/sdk

# Linux
sdk.dir=/home/SEU_USUARIO/Android/Sdk
```

3. Descubra o caminho real do SDK:

   **File → Settings → Languages & Frameworks → Android SDK**

   Copie o campo **Android SDK Location** e cole em `sdk.dir`.

4. Confira se a pasta existe e contém `platform-tools` e `platforms`.
5. Instale pelo menos:
   - **Android SDK Platform 35**
   - **Android SDK Build-Tools**
   - **Android SDK Platform-Tools**
   - **Android Emulator**
6. **File → Sync Project with Gradle Files** (ícone do elefante com seta).

Quando o Sync conclui e o SDK é encontrado, o botão **Run ▶** deixa de ficar cinza *por causa do SDK*.

Modelo pronto: `local.properties.example` (copie para `local.properties` e ajuste o caminho).

### 4.4 Criar um emulador (AVD)

Sem dispositivo selecionado o Play também fica desabilitado.

1. **Tools → Device Manager** (ou ícone de celular na lateral).
2. **+** / **Create Virtual Device**.
3. Hardware: **Pixel 8** (ou qualquer Pixel).
4. System image: **API 35** (VanillaIceCream) — baixe se aparecer *Download*.
5. Finish. No Device Manager, clique em **Play** no AVD.

Com a opção da seção 4.2 marcada, o emulador abre **dentro** de **Running Devices**.

### 4.5 Rodar o app

1. Na barra superior, o combo ao lado do ▶ deve mostrar o AVD (ex.: *Pixel 8 API 35*) ou *Running Devices*.
2. O módulo deve ser **app**.
3. Clique no **▶ Run 'app'** (`Shift+F10` no Windows/Linux, `⌃R` no macOS).
4. A aba **Build** deve chegar em *BUILD SUCCESSFUL*.
5. A `MainActivity` aparece no emulador embutido.

---

## Problemas comuns (botão Run cinza / emulador fora da aba)

| Sintoma | Causa | O que fazer |
| --- | --- | --- |
| *SDK location not found. Define location with sdk.dir in the local.properties file* | `local.properties` ausente ou `sdk.dir` errado | Criar o arquivo, colar o caminho do Android SDK, Sync |
| Run ▶ cinza, combo de device vazio | Nenhum AVD / aparelho | Device Manager → criar/iniciar AVD, ou ligar USB debugging num celular |
| Sync falha com JDK | Projeto pede Java 11/17 | **Settings → Build, Execution, Deployment → Build Tools → Gradle → Gradle JDK** = 17 |
| Emulador abre em janela solta | Opção da IDE desmarcada | **Settings → Tools → Emulator** → marcar *Launch in the Running Devices tool window* |
| Aba Running Devices não aparece | Tool window fechada | **View → Tool Windows → Running Devices** |
| `Unable to locate adb` | Platform-Tools faltando | SDK Manager → instale *Android SDK Platform-Tools* |
| App não instala no emulador | imagem AVD / API incompatível | Recrie o AVD com API 35 |

Depois de corrigir SDK ou JDK: **File → Invalidate Caches… → Invalidate and Restart**, então Sync de novo.

---

## Atalhos úteis no Android Studio

| Ação | Windows / Linux | macOS |
| --- | --- | --- |
| Run | `Shift+F10` | `⌃R` |
| Settings | `Ctrl+Alt+S` | `⌘,` |
| Sync Gradle | clique no elefante | clique no elefante |
| Device Manager | `Tools → Device Manager` | idem |
