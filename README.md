
# Creating a Chatbot with OpenAI API in Kotlin

This repository contains a Kotlin project for creating a chatbot using the OpenAI API. The project is designed to be used in a mobile application, but the example provided is a simple console-based implementation.

## Project Setup

To set up the project, you need to include the necessary dependencies in your `build.gradle.kts` file:

```kotlin
dependencies {
    implementation("com.squareup.okhttp3:okhttp:5.0.0-alpha.12")
    implementation("com.google.code.gson:gson:2.9.1")
}
```

These dependencies include OkHttp for handling HTTP requests and Gson for parsing JSON responses.

## Main Code

Import the necessary libraries at the beginning of your Kotlin file:

```kotlin
import okhttp3.MediaType.Companion.toMediaTypeOrNull
import okhttp3.OkHttpClient
import okhttp3.Request
import okhttp3.RequestBody.Companion.toRequestBody
import com.google.gson.Gson
import com.google.gson.JsonParser
```

These imports allow you to create and execute HTTP requests and handle JSON data.

## Chatbot Implementation

Define key variables such as the API key, model, OpenAI API URL, and instances of OkHttpClient and Gson:

```kotlin
val apikey = "sk-proj-apikey"
val model = "gpt-3.5-turbo-0613"
val url = "https://api.openai.com/v1/chat/completions"
val client = OkHttpClient()
val gson = Gson()
val conversation = mutableListOf<Map<String, String>>()
```

- `apikey`: Your OpenAI API key.
- `model`: The model you want to use from OpenAI.
- `url`: The endpoint for the OpenAI API.
- `client`: An instance of OkHttpClient for making HTTP requests.
- `gson`: An instance of Gson for handling JSON.
- `conversation`: A list to store the conversation history.

## Main Chat Loop

The main chat loop reads user input, sends it to the OpenAI API, and processes the response:

```kotlin
while (true) {
    print("You: ")
    val userInput = readlnOrNull() ?: break
    conversation.add(mapOf("role" to "user", "content" to userInput))
    val requestBodyJson = gson.toJson(mapOf("model" to model, "messages" to conversation))
    val requestBody = requestBodyJson.toRequestBody("application/json".toMediaTypeOrNull())
    val request = Request.Builder()
        .url(url)
        .header("Content-Type", "application/json")
        .header("Accept", "application/json")
        .header("Authorization", "Bearer $apikey")
        .post(requestBody)
        .build()
    val response = client.newCall(request).execute()
    if (response.isSuccessful) {
        val responseBody = response.body.string()
        responseBody.let {
            val jsonResponse = JsonParser.parseString(it).asJsonObject
            val assistantResponse = jsonResponse["choices"].asJsonArray[0].asJsonObject["message"].asJsonObject["content"].asString
            println("Assistant: $assistantResponse")
            conversation.add(mapOf("role" to "assistant", "content" to assistantResponse))
        }
    } else {
        println("Error in the request: ${response.code} ${response.message}")
        break
    }
}
```

- The loop continues to run, reading user input from the console.
- User input is added to the `conversation` list.
- A JSON request body is created and sent to the OpenAI API.
- The response from the API is processed, and the assistant's reply is printed to the console and added to the conversation history.

This code implements a simple console-based chatbot that interacts with the OpenAI API, handling HTTP requests and JSON responses using OkHttp and Gson. For a mobile application, adapt the interaction loop to the appropriate user interface.

## Contributing

Feel free to fork this repository and submit pull requests. For major changes, please open an issue first to discuss what you would like to change.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
