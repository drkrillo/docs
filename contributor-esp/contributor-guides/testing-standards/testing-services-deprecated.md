# Probar Servicios (Obsoleto)

{% hint style="warning" %}
Este es un enfoque obsoleto para probar servicios. Para nuevos servicios, por favor refiérase a [Probar Servicios (Well-Known-Component)](testing-services-wkc.md).
{% endhint %}

Los servicios DEBERÍAN ser probados principalmente usando **pruebas de API**, es decir, un conjunto de pruebas que ejercitan el código del servicio a través de su API levantando el servicio, con sus dependencias externas siendo mockeadas (DBs y APIs). **Las pruebas unitarias** DEBERÍAN incluirse en casos donde se encuentran secciones de utilidad de código con un alto número de casos a ejercitar o código no performante, haciendo imposible o difícil probar con pruebas de API. La razón detrás de esto es que debido a nuestra arquitectura de micro-servicios, que reduce las responsabilidades y así la cantidad de código a probar, la lógica de los servicios es simple, haciendo que las pruebas de API sean la opción más eficiente en tiempo por valor al asegurar que los servicios funcionan correctamente.

## Stack de pruebas

Los servicios DEBEN ser probados usando [Jest](https://jestjs.io/) como el framework principal de pruebas con [supertest](https://github.com/visionmedia/supertest) como el framework para configurar el servidor y probarlo.

## Estructura de directorios

Como los servicios PUEDEN tener tanto pruebas de API como pruebas unitarias, habrá más de un lugar donde las pruebas irán:

* Las pruebas unitarias DEBEN ir junto al archivo o módulo que están probando, con el mismo nombre, pero con la extensión `spec.ts` en lugar de `ts`.
* Las pruebas de API ejercitarán código a lo largo del servicio, por lo que DEBEN colocarse en un directorio `test`, en la ruta raíz. Los archivos se nombrarán como el recurso del servicio que estarán probando, es decir, si las pruebas de API estarán probando el recurso `books`, usualmente bajo `/books`, el nombre del archivo DEBE ser `books.spec.ts` y DEBE colocarse en el directorio `test`.

## Qué probar

* Middlewares aplicados a la ruta (auth, etc), para asegurar que la ruta funciona como se espera.
* La lógica de los controladores, para asegurar que la ruta hace lo que esperamos que haga.

## Probar servicios con API

Como se mencionó antes, las pruebas de API se harán usando Jest y supertest.

Para ilustrar cómo un desarrollador hará pruebas de API, tenemos el siguiente ejemplo:

```tsx
import express from "express"
import { authMiddleware } from "../auth"
import db from "../db"

const app = express()

app.get('/books', async (req, res) => {
	let books: Books[];
	if(req.query.author) {
		books = await db.getFilteredBooks({ author: req.query.author })
	} else {
		books = await db.getBooks()
	}
  res.json(books)
})

app.post('/books', authMiddleware, async (req, res) => {
	await db.insertBook(req.body)
	res.status(200).end()
})
```

Encontramos en el ejemplo una ruta con diferentes métodos HTTP, GET y POST. Ambos endpoints se refieren al mismo recurso, books, por lo que un archivo, llamado `books.spec.ts` DEBE ser creado bajo el directorio `test` para contener las pruebas para ellos.

Todas las rutas y los diferentes flujos de ejecuciones DEBEN ser probados exhaustivamente. Estos son los casos que el desarrollador DEBE probar para estos endpoints:

* Solicitar todos los libros funciona, respondiendo con un 200 y la lista de todos los libros.
* Solicitar todos los libros de un autor dado funciona, respondiendo con un 200 y la lista de todos los libros de ese autor.
* Publicar un libro sin autenticar falla, respondiendo con un 401 y un mensaje de error.
* Publicar un libro autenticado funciona, respondiendo con un 200 e insertando el libro.

Estos casos definitivamente obtendrán sus **it**s y la expectativa de esos its DEBE contener al menos **el código de estado de respuesta esperado** y lo que se **espera en el cuerpo de la respuesta** (si hay alguno).

Así es como estas pruebas de endpoint deberían escribirse:

```tsx
import db from '../db'
import app from '../express_app.ts'
// Solo mockeamos dependencias externas como DBs
jest.mock('../db')

const mockDb = db as jest.Mocked<typeof db>

const server = supertest(app.getApp())

// Esto también podría escribirse como /books?
describe('when requesting the books resource', () => {
	let url: string
	let aBook: Book
	let anotherBook: Book
	beforeEach(() => {
		url = '/books'
		aBook = { title: 'aTitle', author: 'anAuthor' }
		anotherBook = { title: 'anotherTitle', author: 'anotherAuthor' }
	})
	
	describe('when querying the books without an author', () => {
		beforeEach(() => {
			mockDb.getBooks.mockResolvedValueOnce([aBook, anotherBook])
		})

		it('should respond with a 200 and all the books', () => {
			return server
	      .get(url)
	      .expect(200)
	      .then((response) => {
				expect(response.body).toEqual([aBook, anotherBook])
	      })
		})
	})
	
	describe('when querying the books with an author', () => {
		beforeEach(() => {
			mockDb.getFilteredBooks.mockResolvedValueOnce([anotherBook])
		})

		it('should respond with a 200 and all the books of an author', () => {
			return server
	      .get(url)
				.query({ author: 'anotherAuthor' })
	      .expect(200)
	      .then((response) => {
				expect(response.body).toEqual([anotherBook])
	      })
		})
	})
	
	describe('when posting a new book unauthenticated', () => {
		it('should respond with a 401 and an error saying that the user is unauthenticated', () => {
			return server
	      .post(url)
				.send(book)
	      .expect(401)
	      .then((response) => {
				expect(response.body).toEqual({ error: 'Unauthenticated' })
	      })
		})
	})
	
	describe('when posting a new book authenticated', () => {	
		it('should respond with a 200 and insert the book', () => {
			return server
	      .post('/books')
	      .set(createAuthHeaders('post', url))
				.send(aBook)
	      .expect(200)
	      .then(() => {
				// Verifica que el libro fue insertado
	        expect(db.insertBook).toHaveBeenCalledWith(aBook)
	      })
		})
	})
})
```
