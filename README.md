# Fang configuration loader
Inspired by [spf13/viper](https://github.com/spf13/viper), this is intended
to be a minimalistic and opinionated configuration loader library.

## Usage
Most basic example would be:
```go
type Config struct {
	Name string
}

config := fang.New[Config].Load()
```

Which does nothing really.

A slightly more interesting example would be:
```go
type Config struct {
	Name string
}

config := fang.New[Config].
    WithEnvironment(map[string]string {
        "NAME": "Name"
    }).
    Load()
```

This would load the `NAME` environment variable into `config.Name`. There is 
also a `WithAutomaticEnv(prefix string)` which automatically maps enviroment
variables to fields in the struct. For example `NAME` gets bound to `config.Name`
but `USER__NAME` would get bound to `config.User.Name`.

A typical example of how this library would be used looks like this:
```go
func ConfigFromEnvironmentWithPrefix(envPrefix string) (Config, error) {
	loader := fang.New[Config]().
		WithDefault(DefaultConfig()).
		WithAutomaticEnv(envPrefix).
		WithConfigFile(fang.ConfigFileOptions{
			Paths: []string{"$HOME", "."},
			Names: []string{"config"},
			Type:  fang.ConfigFileTypeYaml,
		})

	return loader.Load()
}
```

This specifies default values, automatically maps environment variables to fields 
in the struct and also looks for a `config.yaml` file in either the user home or 
current directory for further configuration.
 