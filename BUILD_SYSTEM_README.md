# EntityCulling Custom Gradle Build System

This is a custom Gradle build system for the EntityCulling mod that replaces the gradle-compose template system.

## Project Structure

```
EntityCulling/
├── settings.gradle              # Multi-module configuration
├── gradle.properties            # Project-wide properties
├── build.gradle                 # Root orchestration with custom tasks
├── gradlew / gradlew.bat        # Gradle wrapper scripts
├── gradle/wrapper/              # Gradle wrapper JAR and properties
├── Shared/
│   ├── build.gradle             # Shared code module (mixins, core logic)
│   └── src/main/
│       ├── java/                # Common code for both loaders
│       └── resources/           # Mixin configs, access wideners
├── EntityCulling-Fabric/
│   ├── build.gradle             # Fabric module with Yarn mappings
│   └── src/main/
│       ├── java/                # Fabric-specific code
│       └── resources/           # fabric.mod.json
└── EntityCulling-Forge/
    ├── build.gradle             # Forge module targeting 47.4.3
    └── src/main/
        ├── java/                # Forge-specific code
        └── resources/           # META-INF/mods.toml
```

## Key Features

### 1. Tri-Module Architecture
- **Shared**: Platform-agnostic code, mixins, and configuration
- **EntityCulling-Fabric**: Fabric implementation with Yarn mappings
- **EntityCulling-Forge**: Forge implementation for version 47.4.3

### 2. Yarn Mappings for Fabric
As requested by user "ya ke yarn", the Fabric module uses Yarn mappings:
```properties
yarn_build=1.20.1+build.10
```

### 3. Forge 47.4.3 Target
The Forge module explicitly targets version 47.4.3:
```properties
forge_full=1.20.1-47.4.3
```

### 4. Occlusion Culling Library Integration
All modules integrate the core dependency:
```properties
occlusion_coordinates=com.logisticscraft:occlusionculling:0.0.7-SNAPSHOT
```

- **Shared**: Exposes as API dependency
- **Fabric**: Embedded via `include`
- **Forge**: Embedded via `jarJar`

## Custom Gradle Tasks

### Build Tasks (entityculling-build)
- `buildAllModules` - Compiles all three modules
- `cleanAllModules` - Removes all build artifacts
- `assembleFabricDistribution` - Packages Fabric mod with dependencies
- `assembleForgeDistribution` - Packages Forge mod with JarJar dependencies
- `generateIntegrationManifest` - Creates detailed module manifest

### Diagnostic Tasks (entityculling-diagnostics)
- `reportModuleVersions` - Displays version information
- `validateOcclusionIntegration` - Verifies library integration
- `inspectConfiguration` - Analyzes module dependencies

### Validation Tasks (entityculling-validation)
- `analyzeMixinClasses` - Validates mixin JSON configuration
- `verifyYarnIntegration` - Confirms Yarn mappings (Fabric)
- `confirmForgeVersion` - Validates Forge 47.4.3 target

## Building

```bash
# Build all modules
./gradlew buildAllModules

# Build specific module
./gradlew :EntityCulling-Fabric:build
./gradlew :EntityCulling-Forge:build

# Clean all
./gradlew cleanAllModules

# Run diagnostics
./gradlew reportModuleVersions
./gradlew validateOcclusionIntegration
```

## Configuration Properties

Edit `gradle.properties` to customize:

```properties
# Core identification
mod_group=dev.tr7zw
mod_version=1.6.2
mod_identifier=entityculling

# Fabric configuration
fabric_loader=0.15.11
fabric_api=0.92.2+1.20.1
yarn_build=1.20.1+build.10

# Forge configuration
forge_full=1.20.1-47.4.3
forge_gradle=6.0.24

# Dependencies
occlusion_coordinates=com.logisticscraft:occlusionculling:0.0.7-SNAPSHOT
```

## Module-Specific Details

### Shared Module
- Contains all mixin classes
- Provides Config and ConfigUpgrader
- Defines Cullable and Provider interfaces
- Used as dependency by both Fabric and Forge

### Fabric Module
- Uses **Yarn mappings** (v2 format)
- Fabric Loom 1.3.61 for dev environment
- Bundles Shared module and occlusion library
- Custom task: `verifyYarnIntegration`

### Forge Module
- Targets **Forge 47.4.3** specifically
- Uses Parchment mappings for better names
- JarJar embedding for dependencies
- Custom task: `confirmForgeVersion`

## Advantages Over gradle-compose

1. **No API rate limits** - Direct Gradle configuration without GitHub API calls
2. **Explicit versioning** - All versions clearly specified in gradle.properties
3. **Custom diagnostics** - Built-in tasks for validation and inspection
4. **Platform-specific optimization** - Yarn for Fabric, Parchment for Forge
5. **Transparent dependency management** - Clear inclusion strategies per platform

## Requirements

- Java 17 or higher
- Gradle 8.5 (via wrapper)
- Internet connection for dependency resolution

## Troubleshooting

### Network Issues
If maven repositories are unavailable, Gradle will cache dependencies after first successful download.

### Mixin Configuration
The `analyzeMixinClasses` task validates that all mixins in `entityculling.mixins.json` have corresponding Java files.

### Version Validation
Run `confirmForgeVersion` and `verifyYarnIntegration` to ensure platform-specific configurations are correct.

## License

This build configuration is part of the EntityCulling project by tr7zw.
