# SHAMA

Shama es un simple wrapper que hace más legible y sencillo el uso de llama.cpp desde una terminal. Simplemente destaca los detalles importantes, busca los modelos en tu carpeta local y actualiza la configuración para configurarlos en `opencode`.

El objetivo de este script es montar un sistema de Inteligencia Artificial de modo que puedas utilizarlo en local para desarrollar mediante opencode (u otro agente) si tienes una buena tarjeta gráfica y recursos de hardware.

```bash
$ shama

███████╗██╗  ██╗ █████╗ ███╗   ███╗ █████╗
██╔════╝██║  ██║██╔══██╗████╗ ████║██╔══██╗
███████╗███████║███████║██╔████╔██║███████║
╚════██║██╔══██║██╔══██║██║╚██╔╝██║██╔══██║
███████║██║  ██║██║  ██║██║ ╚═╝ ██║██║  ██║
╚══════╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝     ╚═╝╚═╝  ╚═╝

Modelos disponibles:

  1. gemma4-26b-a4b-q2-kxl.gguf (10.55 GB)
  2. gemma4-e2b-it-q4xl.gguf (3.17 GB)
  3. gemma4-e4b-it-q8.gguf (8.19 GB)
  4. lfm2.5-350m-q8.gguf (0.45 GB)
  5. nvidia-nemotron3-nano-4b-q4.gguf (2.84 GB)
  6. qwen3.5-27b-ud-iq2-xxs.gguf (8.57 GB)
  7. qwen3.5-9b-iq4-nl.gguf (5.37 GB)
  8. qwen3.6-27b-ud-iq2-xxs.gguf (9.39 GB)
  9. qwen3.6-35b-a3b-iq2_xxs.gguf (10.76 GB)
  0. Salir

Elige un modelo [1-9]:
```

## Requisitos

Antes de empezar, necesitas tener el software instalado en tu sistema, para asi poder utilizar llama.cpp en WSL2 o en Docker, compartiendo la GPU del anfitrión:

- [Docker](https://docs.docker.com/desktop/setup/install/windows-install/) instalado
- [WSL2](https://terminaldelinux.com/terminal/wsl/instalacion-wsl/) instalado (Windows)
- [Drivers de NVIDIA](https://www.nvidia.com/es-es/drivers/) instalados
- [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Debian&target_version=13&target_type=deb_local) para usar GPU en WSL2 (Windows) ~4GB
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html#with-apt-ubuntu-debian) Instalado para compartir GPU con Docker
- [Node] instalado para ejecutar el script

## Instalación de llama.cpp

Con esto, compilas `llama.cpp` específicamente para tu hardware y así conseguir el mejor rendimiento. En esta configuración lo hace con CUDA (nvidia). Si necesitas otro, echa un vistazo a [docs](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md):

```bash
sudo apt install git build-essential cmake libssl-dev wget jq
git clone https://github.com/ggml-org/llama.cpp.git
cd llama.cpp
cmake -B build -DGGML_CUDA=ON
cmake --build build --config Release -j 8
```

Una vez compilado, los archivos se habrán generado en `build/bin`. Para instalarlos puedes hacer lo siguiente:

```bash
sudo install -Dm755 build/bin/* /usr/local/bin/
sudo install -Dm644 build/bin/*.so /usr/local/lib/
sudo ldconfig
```

Ahora, simplemente escribiendo `llama-server --version` deberías ver algo similar a esto (pero con tu hardware):

```bash
ggml_cuda_init: found 1 CUDA devices (Total VRAM: 12287 MiB):
  Device 0: NVIDIA GeForce RTX 3060, compute capability 8.6, VMM: yes, VRAM: 12287 MiB
version: 8931 (9725a313b)
built with GNU 14.2.0 for Linux x86_64
```

## Descarga de modelos

En [huggingface](https://huggingface.co) tienes muchísimos repositorios de modelos de inteligencia artificial para utilizar. Te recomiendo especialmente los de [UnSloth](https://huggingface.co/unsloth).

Descarga los modelos `.gguf` que necesites y guardalos en la carpeta `~/.models/gguf`. El script `shama` los buscará ahí (o donde tu configures).

## Instalación de shama

El fichero `shama` de este repo es un wrapper/script para que sea más sencillo trabajar con `llama`. Simplemente nos descargamos el fichero y lo instalamos en nuestro sistema:

```bash
cp shama /usr/local/bin/
chmod +x /usr/local/bin/shama
```

Asegúrate de tener un fichero de configuración de opencode, generalmente en `~/.config/opencode/opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "llamacpp/qwen3.6-35b-a3b-iq2_xss",
  "provider": {
    "llamacpp": {
      "models": {
      },
      "name": "Llama.cpp",
      "npm": "@ai-sdk/openai-compatible",
      "options": {
        "baseURL": "http://127.0.0.1:8999/v1"
      }
    }
  }
}
```

Observa que `model` es el modelo que quieres que salga por defecto, en este caso añadimos [Qwen 3.6 35B A3B, que es el mostrado en este video](https://youtu.be/Vvb_KYCTD2w). Luego, asegúrate de tener el puerto correcto ( `:8999` ) en `baseURL`, por si lo has cambiado en el script `shama`.

Más información:

[![](https://img.youtube.com/vi/Vvb_KYCTD2w/sddefault.jpg)](https://youtu.be/Vvb_KYCTD2w)
