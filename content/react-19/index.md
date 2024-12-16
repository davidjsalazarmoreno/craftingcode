---
title: "Lo nuevo en React 19"
date: 2024-06-11T14:30:58-05:00
draft: false
description: "Lo nuevo en React 19"
---

## Actions

### Antes

Los actions buscan simplificar el código de React, veamos un caso muy común: una mutación de datos y luego actualizar el estado en respuesta.

```tsx
// Before Actions
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, setIsPending] = useState(false);

  const handleSubmit = async () => {
    setIsPending(true);
    const error = await updateName(name);
    setIsPending(false);
    if (error) {
      setError(error);
      return;
    } 
    redirect("/path");
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}
```

Donde manejamos varias cosas manualmente, como el estado pendiente o el manejo de errores.

### Después

React 19 permite pasarle una función asíncrona al hook `useTransition`, lo que nos permite manejar los estados pendientes, errores y formularios de forma automática.

```tsx
"use client";
import { redirect } from "next/navigation";
import { useState, useTransition } from "react";

export default function FormAction() {
  const [name, setName] = useState("");
  const [error, setError] = useState("");
  const [isPending, startTransition] = useTransition();

  const updateName = (name: string): Promise<string> => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("Error updating name");
        console.info(name);
      }, 3000);
    });
  };

  const handleSubmit = () => {
    startTransition(async () => {
      const error = await updateName(name);

      if (error.length > 0) {
        setError(error);
        return;
      }

      console.log("Name updated!");
      redirect("/success")
    });
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {isPending && <p>Updating name...</p>}
      {error && <p>{error}</p>}
    </div>
  );
}
```

Primero, pasamos la función asíncrona a `startTransition`, en este ejemplo si esta función devuelve un error lo mostramos, en caso contrario redirigimos a la página de éxito.

Y en medio de todo esto tenemos el estado `isPending` con el que mostramos un indicador de carga.


Aun así sigue siendo mucho código por ejemplo el error aún está siendo manejado manualmente.

##  useActionState
 

En el [video anterior](https://www.tiktok.com/@craftingcode/video/7379677416744029445?lang=es) vimos cómo usando useTransition podíamos manejar un estado pendiente para formularios, pero aún estábamos manejando el estado de error manualmente con useState.

```tsx
  const handleSubmit = () => {
    startTransition(async () => {
      const error = await updateName(name);

      if (error.length > 0) {
        setError(error);
        return;
      }

      console.log("Name updated!");
      redirect("/success")
    });
  };
```
  

React 19 introduce un nuevo hook llamado useActionState que nos deja actualizar el estado basado en el resultado de una acción de formulario.

  
El hook recibe dos parámetros, una función (que puede ser asíncrona) y un objeto que es el estado inicial de la acción.

  
La función es llamada cada vez que hacemos submit al formulario, si hacemos submit y revisamos el previous state veremos que es igual a “*no hay nombre*” (estado inicial).
  

Si llamamos las subsecuentes veces previousState debería ser igual al nombre que escribimos en el formulario.

  ```tsx
"use client";
import { useRouter } from "next/navigation";
import { useActionState } from "react";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";

type ActionState = string;

export default function ActionState() {
  const router = useRouter();

  const updateName = (name: string): Promise<string> => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(name);
      }, 1000);
    });
  };

  const [error, submitAction, pending] = useActionState<string | null, FormData>(
    async (previousState, formData: FormData) => {
      try {
        
        console.log("previousState")
        console.log(previousState)
        const name = formData.get("name") as string;
        const error = await updateName(name);

        if (error.length > 0) {
          return error;
        } else {
          return null;
        }
      } catch (e) {
        return "Error";
      }
    },
    "No hay nombre"
  );

  return (
    <form
      action={submitAction}
      className="flex flex-col w-1/2 my-0 mx-auto p-5 gap-3"
    >
      <Input
        type="text"
        name="name"
        placeholder="Type your name"
        disabled={pending}
      />
      <Button type="submit" disabled={pending}>
        Update
      </Button>
      {error && !pending && <p>{error}</p>}
      {pending && <p>Loading</p>}
    </form>
  );
}

```

  

Aja ¿y cómo conectamos este hook al formulario?, fácil el hook retorna tres valores: el estado actual del formulario, una función de acción y un boleano de carga al que llamaremos `isPending`.

```tsx
"use client";
import { useRouter } from "next/navigation";
import { useActionState } from "react";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";

type ActionState = string;

export default function ActionState() {
  const router = useRouter();

  const updateName = (name: string): Promise<string> => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(name);
      }, 1000);
    });
  };

  const [error, submitAction, pending] = useActionState<
    string | null,
    FormData
  >(async (_, formData: FormData) => {
    try {
      const name = formData.get("name") as string;
      await updateName(name);

      router.push("/");
      return null;
    } catch (e) {
      return "Error";
    }
  }, "No hay nombre");

  return (
    <form
      action={submitAction}
      className="flex flex-col w-1/2 my-0 mx-auto p-5 gap-3"
    >
      <Input
        type="text"
        name="name"
        placeholder="Type your name"
        disabled={pending}
      />
      <Button type="submit" disabled={pending}>
        Update
      </Button>
      {error && !pending && <p>{error}</p>}
      {pending && <p>Loading</p>}
    </form>
  );
}

```
  

La función de acción es la que nos interesa, a partir de React 19 los formularios pueden recibir una acción como función.

  
En el ejemplo anterior tenemos tenemos el estado de carga, el error mostrado y la redirección en caso de éxito.


---

{{< newsletter >}}