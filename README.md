import React, { useState } from "react";
import axios from "axios";
import Select from "react-select";

const options = [
  { value: "alphabets", label: "Alphabets" },
  { value: "numbers", label: "Numbers" },
  { value: "highestAlphabet", label: "Highest Alphabet" },
];

const App = () => {
  const [jsonInput, setJsonInput] = useState("");
  const [error, setError] = useState(null);
  const [selectedOptions, setSelectedOptions] = useState([]);
  const [responseData, setResponseData] = useState(null);

  const handleSubmit = async () => {
    try {
      const parsedInput = JSON.parse(jsonInput);
      setError(null);

      const response = await axios.post("http://localhost:5000/api/process", parsedInput);
      setResponseData(response.data);
    } catch (err) {
      setError("Invalid JSON input");
    }
  };

  const filterResponse = () => {
    if (!responseData) return null;
    
    let filteredData = {};
    if (selectedOptions.some(option => option.value === "alphabets")) {
      filteredData.alphabets = responseData.alphabets;
    }
    if (selectedOptions.some(option => option.value === "numbers")) {
      filteredData.numbers = responseData.numbers;
    }
    if (selectedOptions.some(option => option.value === "highestAlphabet")) {
      filteredData.highestAlphabet = responseData.highestAlphabet;
    }
    return filteredData;
  };

  return (
    <div className="min-h-screen flex flex-col items-center p-6 bg-gray-100">
      <h1 className="text-2xl font-bold mb-4">Frontend JSON Processor</h1>
      <textarea
        className="w-1/2 p-2 border rounded"
        rows="5"
        placeholder='Enter JSON, e.g. { "data": ["A","C","z"] }'
        value={jsonInput}
        onChange={(e) => setJsonInput(e.target.value)}
      ></textarea>
      <button
        className="mt-3 px-4 py-2 bg-blue-500 text-white rounded"
        onClick={handleSubmit}
      >
        Submit
      </button>
      {error && <p className="text-red-500 mt-2">{error}</p>}
      {responseData && (
        <div className="mt-4 w-1/2">
          <Select
            options={options}
            isMulti
            onChange={setSelectedOptions}
            className="mb-4"
          />
          <div className="p-4 bg-white border rounded">
            <pre>{JSON.stringify(filterResponse(), null, 2)}</pre>
          </div>
        </div>
      )}
    </div>
  );
};

export default App;
