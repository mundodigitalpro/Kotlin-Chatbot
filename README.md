# Creating a Chatbot with OpenAI API in Kotlin

To create a chatbot using the OpenAI API in a mobile app with Kotlin, follow these steps:

## Project Setup:
- In your `build.gradle.kts` file, add the following dependencies:
  ```kotlin
  dependencies {
      implementation("com.squareup.okhttp3:okhttp:5.0.0-alpha.12")
      implementation("com.google.code.gson:gson:2.9.1")
  }
  ```

## Main Code:
- Import the necessary libraries:
  ```kotlin
  import okhttp3.MediaType.Companion.toMediaTypeOrNull
  import okhttp3.OkHttpClient
  import okhttp3.Request
  import okhttp3.RequestBody.Companion.toRequestBody
  import com.google.gson.Gson
  import com.google.gson.JsonParser
  ```

## Chatbot Implementation:
- Define key variables such as the API key, model, and the OpenAI API URL:
  ```kotlin
  val apikey = "sk-proj-apikey"
  val model = "gpt-3.5-turbo-0613"
  val url = "https://api.openai.com/v1/chat/completions"
  val client = OkHttpClient()
  val gson = Gson()
  val conversation = mutableListOf<Map<String, String>>()
  ```

## Main Chat Loop:
- Read the user input and add the conversation to the history:
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

This code implements a simple console-based chatbot that interacts with the OpenAI API, handling HTTP requests and JSON responses using OkHttp and Gson. For a mobile application, adapt the interaction loop to the appropriate user interface.
