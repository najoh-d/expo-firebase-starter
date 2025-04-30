import { useState, useEffect } from "react"
import { View, Text, StyleSheet, TextInput, TouchableOpacity, ActivityIndicator, CheckBox } from "react-native"
import { useAuth } from "../context/AuthContext"
import { useNavigation } from "@react-navigation/native"
import { LinearGradient } from "expo-linear-gradient"
import { Ionicons } from "@expo/vector-icons"
import * as SecureStore from "expo-secure-store"

export default function LoginScreen() {
  const [username, setUsername] = useState("")
  const [password, setPassword] = useState("")
  const [error, setError] = useState("")
  const [isLoading, setIsLoading] = useState(false)
  const [rememberMe, setRememberMe] = useState(false)
  const { login, isLoggedIn } = useAuth()
  const navigation = useNavigation()

  useEffect(() => {
    // Check if credentials are stored and auto-fill them
    const loadStoredCredentials = async () => {
      const storedUsername = await SecureStore.getItemAsync("username")
      const storedPassword = await SecureStore.getItemAsync("password")

      if (storedUsername && storedPassword) {
        setUsername(storedUsername)
        setPassword(storedPassword)
        setRememberMe(true)
      }
    }

    loadStoredCredentials()

    if (isLoggedIn) {
      navigation.reset({
        index: 0,
        routes: [{ name: "Dashboard" }],
      })
    }
  }, [isLoggedIn, navigation])

  const handleLogin = async () => {
    if (!username || !password) {
      setError("Please enter both username and password")
      return
    }

    setIsLoading(true)
    setError("")

    try {
      await login(username, password)

      // Save credentials securely if "Remember Me" is checked
      if (rememberMe) {
        await SecureStore.setItemAsync("username", username)
        await SecureStore.setItemAsync("password", password)
      } else {
        await SecureStore.deleteItemAsync("username")
        await SecureStore.deleteItemAsync("password")
      }

      // Navigate to the Dashboard after successful login
      navigation.reset({
        index: 0,
        routes: [{ name: "Dashboard" }],
      })
    } catch (error) {
      setError("Invalid username or password")
    } finally {
      setIsLoading(false)
    }
  }

  return (
    <LinearGradient colors={["#111827", "#000000"]} style={styles.container}>
      <View style={styles.content}>
        <View style={styles.header}>
          <Ionicons name="airplane" size={40} color="#ef4444" />
          <Text style={styles.title}>Aviator Predictor</Text>
          <Text style={styles.subtitle}>Login to access predictions for BetPawa</Text>
        </View>

        <View style={styles.form}>
          <View style={styles.inputContainer}>
            <Text style={styles.label}>Username</Text>
            <TextInput
              style={styles.input}
              placeholder="Enter your username"
              placeholderTextColor="#9ca3af"
              value={username}
              onChangeText={setUsername}
              autoCapitalize="none"
            />
          </View>

          <View style={styles.inputContainer}>
            <Text style={styles.label}>Password</Text>
            <TextInput
              style={styles.input}
              placeholder="Enter your password"
              placeholderTextColor="#9ca3af"
              value={password}
              onChangeText={setPassword}
              secureTextEntry
            />
          </View>

          {error ? <Text style={styles.errorText}>{error}</Text> : null}

          <View style={styles.checkboxContainer}>
            <CheckBox
              value={rememberMe}
              onValueChange={setRememberMe}
              color="#ef4444"
            />
            <Text style={styles.checkboxLabel}>Remember Me</Text>
          </View>

          <TouchableOpacity style={styles.button} onPress={handleLogin} disabled={isLoading}>
            {isLoading ? <ActivityIndicator color="#ffffff" /> : <Text style={styles.buttonText}>Login</Text>}
          </TouchableOpacity>

          <View style={styles.registerLinkContainer}>
            <Text style={styles.registerText}>Don't have an account?</Text>
            <TouchableOpacity onPress={() => navigation.navigate("Register")}>
              <Text style={styles.registerLink}>Sign up</Text>
            </TouchableOpacity>
          </View>
        </View>
      </View>
    </LinearGradient>
  )
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    padding: 20,
  },
  content: {
    width: "100%",
    maxWidth: 400,
  },
  header: {
    alignItems: "center",
    marginBottom: 30,
  },
  title: {
    fontSize: 28,
    fontWeight: "bold",
    color: "white",
    marginTop: 10,
  },
  subtitle: {
    fontSize: 16,
    color: "#9ca3af",
    marginTop: 8,
  },
  form: {
    backgroundColor: "#1f2937",
    borderRadius: 12,
    padding: 20,
    shadowColor: "#000",
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.25,
    shadowRadius: 3.84,
    elevation: 5,
  },
  inputContainer: {
    marginBottom: 16,
  },
  label: {
    fontSize: 16,
    fontWeight: "500",
    color: "white",
    marginBottom: 8,
  },
  input: {
    backgroundColor: "#374151",
    borderRadius: 8,
    padding: 12,
    color: "white",
    fontSize: 16,
  },
  button: {
    backgroundColor: "#ef4444",
    borderRadius: 8,
    padding: 15,
    alignItems: "center",
    marginTop: 10,
  },
  buttonText: {
    color: "white",
    fontSize: 16,
    fontWeight: "600",
  },
  errorText: {
    color: "#ef4444",
    marginBottom: 10,
    fontSize: 14,
  },
  checkboxContainer: {
    flexDirection: "row",
    alignItems: "center",
    marginBottom: 20,
  },
  checkboxLabel: {
    color: "white",
    marginLeft: 8,
  },
  registerLinkContainer: {
    flexDirection: "row",
    justifyContent: "center",
    marginTop: 20,
  },
  registerText: {
    color: "#9ca3af",
    fontSize: 14,
  },
  registerLink: {
    color: "#ef4444",
    fontSize: 14,
    fontWeight: "600",
    marginLeft: 5,
  },
})"use client"

import { useState, useEffect } from "react"
import { View, Text, StyleSheet, TextInput, TouchableOpacity, ActivityIndicator } from "react-native"
import { useAuth } from "../context/AuthContext"
import { useNavigation } from "@react-navigation/native"
import { LinearGradient } from "expo-linear-gradient"
import { Ionicons } from "@expo/vector-icons"

export default function LoginScreen() {
  const [username, setUsername] = useState("")
  const [password, setPassword] = useState("")
  const [error, setError] = useState("")
  const [isLoading, setIsLoading] = useState(false)
  const { login, isLoggedIn } = useAuth()
  const navigation = useNavigation()

  useEffect(() => {
    if (isLoggedIn) {
      navigation.reset({
        index: 0,
        routes: [{ name: "Dashboard" as never }],
      })
    }
  }, [isLoggedIn, navigation])

  const handleLogin = async () => {
    if (!username || !password) {
      setError("Please enter both username and password")
      return
    }

    setIsLoading(true)
    setError("")

    try {
      await login(username, password)
      // Navigation will happen automatically due to the useEffect
    } catch (error) {
      setError("Invalid username or password")
    } finally {
      setIsLoading(false)
    }
  }

  return (
    <LinearGradient colors={["#111827", "#000000"]} style={styles.container}>
      <View style={styles.content}>
        <View style={styles.header}>
          <Ionicons name="airplane" size={40} color="#ef4444" />
          <Text style={styles.title}>Aviator Predictor</Text>
          <Text style={styles.subtitle}>Login to access predictions for BetPawa</Text>
        </View>

        <View style={styles.form}>
          <View style={styles.inputContainer}>
            <Text style={styles.label}>Username</Text>
            <TextInput
              style={styles.input}
              placeholder="Enter your username"
              placeholderTextColor="#9ca3af"
              value={username}
              onChangeText={setUsername}
              autoCapitalize="none"
            />
          </View>

          <View style={styles.inputContainer}>
            <Text style={styles.label}>Password</Text>
            <TextInput
              style={styles.input}
              placeholder="Enter your password"
              placeholderTextColor="#9ca3af"
              value={password}
              onChangeText={setPassword}
              secureTextEntry
            />
          </View>

          {error ? <Text style={styles.errorText}>{error}</Text> : null}

          <TouchableOpacity style={styles.button} onPress={handleLogin} disabled={isLoading}>
            {isLoading ? <ActivityIndicator color="#ffffff" /> : <Text style={styles.buttonText}>Login</Text>}
          </TouchableOpacity>

          <View style={styles.registerLinkContainer}>
            <Text style={styles.registerText}>Don't have an account?</Text>
            <TouchableOpacity onPress={() => navigation.navigate("Register" as never)}>
              <Text style={styles.registerLink}>Sign up</Text>
            </TouchableOpacity>
          </View>
        </View>
      </View>
    </LinearGradient>
  )
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    padding: 20,
  },
  content: {
    width: "100%",
    maxWidth: 400,
  },
  header: {
    alignItems: "center",
    marginBottom: 30,
  },
  title: {
    fontSize: 28,
    fontWeight: "bold",
    color: "white",
    marginTop: 10,
  },
  subtitle: {
    fontSize: 16,
    color: "#9ca3af",
    marginTop: 8,
  },
  form: {
    backgroundColor: "#1f2937",
    borderRadius: 12,
    padding: 20,
    shadowColor: "#000",
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.25,
    shadowRadius: 3.84,
    elevation: 5,
  },
  inputContainer: {
    marginBottom: 16,
  },
  label: {
    fontSize: 16,
    fontWeight: "500",
    color: "white",
    marginBottom: 8,
  },
  input: {
    backgroundColor: "#374151",
    borderRadius: 8,
    padding: 12,
    color: "white",
    fontSize: 16,
  },
  button: {
    backgroundColor: "#ef4444",
    borderRadius: 8,
    padding: 15,
    alignItems: "center",
    marginTop: 10,
  },
  buttonText: {
    color: "white",
    fontSize: 16,
    fontWeight: "600",
  },
  errorText: {
    color: "#ef4444",
    marginBottom: 10,
    fontSize: 14,
  },
  registerLinkContainer: {
    flexDirection: "row",
    justifyContent: "center",
    marginTop: 20,
  },
  registerText: {
    color: "#9ca3af",
    fontSize: 14,
  },
  registerLink: {
    color: "#ef4444",
    fontSize: 14,
    fontWeight: "600",
    marginLeft: 5,
  },
})
