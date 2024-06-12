---
title: "Lo nuevo en React 19"
date: 2024-06-11T14:30:58-05:00
draft: false
description: "Lo nuevo en React 19"
---

## Actions

### Antes

Los actions buscan simplificar el código de React, veamos un caso muy común: una mutación de datos y luego actualizar el estado en respuesta.

```jsx
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

```jsx
"use client";
import { redirect } from "next/navigation";
import { useState, useTransition } from "react";

export default function FormAction() {
  const [name, setName] = useState("");
  const [error, setError] = useState("");
  const [isPending, startTransition] = useTransition();

  const updateName = (name: string) => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        reject("Error updating name");
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

En el siguiente artículo veremos otro escenario: **las actualizaciones optimistas con lo que simplificamos este código.**.


