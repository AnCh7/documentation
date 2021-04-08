### Viper

> References:
>
> https://github.com/spf13/viper



Viper is a complete configuration solution for Go applications including 12-Factor apps.

1. Find, load, and unmarshal a configuration file in JSON, TOML, YAML, HCL, INI, envfile or Java properties formats.
2. Provide a mechanism to set default values for your different configuration options.
3. Provide a mechanism to set override values for options specified through command line flags.
4. Provide an alias system to easily rename parameters without breaking existing code.
5. Make it easy to tell the difference between when a user has provided a command line or config file which is the same as the default.
6. Viper is designed to be a [companion](http://en.wikipedia.org/wiki/Viper_(G.I._Joe)) to [Cobra](https://github.com/spf13/cobra).

Establishing Defaults

```go
viper.SetDefault("ContentDir", "content")
```

Reading Config Files

```go
viper.SetConfigName("config") // name of config file (without extension)
viper.AddConfigPath("/etc/appname/")   // path to look for the config file in
err := viper.ReadInConfig() // Find and read the config file
if err != nil { // Handle errors reading the config file
	panic(fmt.Errorf("Fatal error config file: %s \n", err))
}
```

Writing Config Files

```go
viper.WriteConfig() // writes current config to predefined path set by 'viper.AddConfigPath()' and 'viper.SetConfigName'
viper.SafeWriteConfig()
```

Watching and re-reading config files

```go
viper.WatchConfig()
viper.OnConfigChange(func(e fsnotify.Event) {
	fmt.Println("Config file changed:", e.Name)
})
```

Setting Overrides

```go
viper.Set("Verbose", true)
```

Registering and Using Aliases

```go
viper.RegisterAlias("loud", "Verbose")
```

Working with Environment Variables

- `AutomaticEnv()`
- `BindEnv(string...) : error`
- `SetEnvPrefix(string)`
- `SetEnvKeyReplacer(string...) *strings.Replacer`
- `AllowEmptyEnv(bool)`

Working with Flags

Viper has the ability to bind to flags. Specifically, Viper supports `Pflags` as used in the [Cobra](https://github.com/spf13/cobra) library.

```go
serverCmd.Flags().Int("port", 1138, "Port to run Application server on")
viper.BindPFlag("port", serverCmd.Flags().Lookup("port"))
```

Remote Key/Value Store Support

```go
import _ "github.com/spf13/viper/remote"
```

Viper will read a config string (as JSON, TOML, YAML, HCL or envfile) retrieved from a path in a Key/Value store such as etcd or Consul.

Viper uses [crypt](https://github.com/bketelsen/crypt) to retrieve configuration from the K/V store, which means that you can store your configuration values encrypted and have them automatically decrypted if you have the correct gpg keyring.  Encryption is optional.

For example, etcd:

```go
viper.AddRemoteProvider("etcd", "http://127.0.0.1:4001","/config/hugo.json")
viper.SetConfigType("json")
err := viper.ReadRemoteConfig()
```

Getting Values From Viper

- `Get(key string) : interface{}`
- `GetBool(key string) : bool`
- `GetFloat64(key string) : float64`
- `GetInt(key string) : int`
- `GetIntSlice(key string) : []int`
- `GetString(key string) : string`
- `GetStringMap(key string) : map[string]interface{}`
- `GetStringMapString(key string) : map[string]string`
- `GetStringSlice(key string) : []string`
- `GetTime(key string) : time.Time`
- `GetDuration(key string) : time.Duration`
- `IsSet(key string) : bool`
- `AllSettings() : map[string]interface{}`

Unmarshaling

- `Unmarshal(rawVal interface{}) : error`
- `UnmarshalKey(key string, rawVal interface{}) : error`