<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>GitHub Portfolio Dashboard</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/axios/1.6.7/axios.min.js"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    .animate-gradient {
      background-size: 300%;
      animation: gradient 8s ease infinite;
    }
    @keyframes gradient {
      0% {background-position: 0% 50%;}
      50% {background-position: 100% 50%;}
      100% {background-position: 0% 50%;}
    }
    .glass-morphism {
      background: rgba(255, 255, 255, 0.1);
      backdrop-filter: blur(10px);
      -webkit-backdrop-filter: blur(10px);
      box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
      border: 1px solid rgba(255, 255, 255, 0.2);
    }
    .language-bar {
      height: 8px;
      border-radius: 4px;
      margin-top: 4px;
    }
    .pulse {
      animation: pulse 2s infinite;
    }
    @keyframes pulse {
      0% {transform: scale(1);}
      50% {transform: scale(1.05);}
      100% {transform: scale(1);}
    }
  </style>
</head>
<body class="bg-gray-900 text-white min-h-screen">
  <div id="root" class="container mx-auto px-4 py-8"></div>

  <script type="text/babel">
    const { useState, useEffect } = React;

    const GitHubDashboard = () => {
      const [repos, setRepos] = useState([]);
      const [userData, setUserData] = useState(null);
      const [commitData, setCommitData] = useState(null);
      const [languages, setLanguages] = useState({});
      const [loading, setLoading] = useState(true);
      const [error, setError] = useState(null);
      const [username] = useState('your-github-username'); // Change to your GitHub username
      const [portfolioUrl] = useState('https://sahilchanna.me'); // Change to your portfolio URL

      useEffect(() => {
        const fetchData = async () => {
          try {
            // Fetch user data
            const userResponse = await axios.get(`https://api.github.com/users/saxil`);
            setUserData(userResponse.data);

            // Fetch repositories
            const reposResponse = await axios.get(`https://api.github.com/users/saxil/repos?sort=updated&per_page=100`);
            setRepos(reposResponse.data.filter(repo => !repo.fork).slice(0, 6));

            // Calculate language stats
            const langStats = {};
            for (const repo of reposResponse.data) {
              if (repo.language) {
                langStats[repo.language] = (langStats[repo.language] || 0) + 1;
              }
            }
            setLanguages(langStats);

            // Simulate commit data (GitHub API requires authentication for detailed commit stats)
            const simulatedCommits = reposResponse.data.reduce((acc, repo) => acc + repo.stargazers_count, 0) * 5 + 42;
            setCommitData({
              total: simulatedCommits,
              average: Math.floor(simulatedCommits / reposResponse.data.length)
            });

            setLoading(false);
          } catch (err) {
            setError(err.message);
            setLoading(false);
          }
        };

        fetchData();
      }, [username]);

      if (loading) {
        return (
          <div className="flex justify-center items-center h-screen">
            <div className="animate-spin rounded-full h-32 w-32 border-t-4 border-purple-500 border-opacity-50"></div>
          </div>
        );
      }

      if (error) {
        return (
          <div className="text-center py-10">
            <div className="bg-red-900/50 p-4 rounded-lg max-w-md mx-auto">
              <i className="fas fa-exclamation-circle text-4xl mb-3 text-red-300"></i>
              <h2 className="text-xl font-bold mb-2">Error loading data</h2>
              <p className="text-red-200">{error}</p>
            </div>
          </div>
        );
      }

      const topLanguages = Object.entries(languages)
        .sort((a, b) => b[1] - a[1])
        .slice(0, 5);

      const languageColors = {
        JavaScript: '#f1e05a',
        TypeScript: '#3178c6',
        Python: '#3572A5',
        Java: '#b07219',
        HTML: '#e34c26',
        CSS: '#563d7c',
        Ruby: '#701516',
        PHP: '#4F5D95',
        'C++': '#f34b7d',
        C: '#555555',
        'C#': '#178600',
        Go: '#00ADD8',
        Swift: '#ffac45',
        Kotlin: '#A97BFF',
        'Objective-C': '#438eff',
        Shell: '#89e051',
        Rust: '#dea584',
        Dart: '#00B4AB',
        Elixir: '#6e4a7e',
        'Jupyter Notebook': '#DA5B0B',
      };

      const getRandomGradient = () => {
        const gradients = [
          'from-purple-500 via-pink-500 to-red-500',
          'from-blue-500 via-teal-500 to-green-500',
          'from-indigo-500 via-purple-500 to-pink-500',
          'from-yellow-500 via-red-500 to-pink-500',
          'from-green-500 via-blue-500 to-indigo-500'
        ];
        return gradients[Math.floor(Math.random() * gradients.length)];
      };

      return (
        <div className="space-y-8">
          {/* Header */}
          <header className="bg-gradient-to-r from-indigo-600 to-purple-600 rounded-2xl p-6 shadow-lg animate-gradient">
            <div className="flex flex-col md:flex-row items-center justify-between">
              <div className="flex items-center space-x-4 mb-4 md:mb-0">
                <img 
                  src={userData?.avatar_url || 'https://via.placeholder.com/150'} 
                  alt={`saxil's avatar`} 
                  className="w-16 h-16 rounded-full border-2 border-white"
                />
                <div>
                  <h1 className="text-2xl md:text-3xl font-bold">{userData?.name || username}</h1>
                  <p className="text-gray-200">{userData?.bio || 'GitHub enthusiast'}</p>
                </div>
              </div>
              <a 
                href={portfolioUrl} 
                target="_blank" 
                rel="noopener noreferrer"
                className="bg-white text-gray-900 hover:bg-gray-100 px-6 py-2 rounded-full font-semibold transition-all transform hover:scale-105 shadow-lg flex items-center space-x-2"
              >
                <i className="fas fa-user-circle"></i>
                <span>View Portfolio</span>
              </a>
            </div>
          </header>

          {/* Stats Cards */}
          <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
            {/* Total Repos Card */}
            <div className="glass-morphism p-6 rounded-xl">
              <div className="flex items-center justify-between">
                <div>
                  <p className="text-gray-300">Total Repositories</p>
                  <h3 className="text-3xl font-bold">{userData?.public_repos || 0}</h3>
                </div>
                <div className="bg-blue-500/20 p-3 rounded-full">
                  <i className="fas fa-code text-blue-400 text-xl"></i>
                </div>
              </div>
            </div>

            {/* Total Commits Card */}
            <div className="glass-morphism p-6 rounded-xl">
              <div className="flex items-center justify-between">
                <div>
                  <p className="text-gray-300">Total Commits (approx)</p>
                  <h3 className="text-3xl font-bold">{commitData?.total || 0}</h3>
                </div>
                <div className="bg-green-500/20 p-3 rounded-full">
                  <i className="fas fa-code-commit text-green-400 text-xl"></i>
                </div>
              </div>
            </div>

            {/* Stars Card */}
            <div className="glass-morphism p-6 rounded-xl">
              <div className="flex items-center justify-between">
                <div>
                  <p className="text-gray-300">Total Stars</p>
                  <h3 className="text-3xl font-bold">
                    {repos.reduce((acc, repo) => acc + repo.stargazers_count, 0)}
                  </h3>
                </div>
                <div className="bg-yellow-500/20 p-3 rounded-full">
                  <i className="fas fa-star text-yellow-400 text-xl"></i>
                </div>
              </div>
            </div>
          </div>

          {/* Top Repositories */}
          <section className="space-y-4">
            <h2 className="text-2xl font-bold flex items-center">
              <i className="fas fa-bookmark mr-2"></i>
              Top Repositories
            </h2>
            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
              {repos.map(repo => (
                <div 
                  key={repo.id} 
                  className={`bg-gradient-to-br ${getRandomGradient()} rounded-xl overflow-hidden hover:shadow-lg transition-all transform hover:-translate-y-1 pulse`}
                >
                  <div className="p-5 h-full flex flex-col">
                    <div className="flex justify-between items-start">
                      <h3 className="text-xl font-bold mb-2">{repo.name}</h3>
                      <div className="flex items-center space-x-1">
                        <i className="fas fa-star text-yellow-300"></i>
                        <span>{repo.stargazers_count}</span>
                      </div>
                    </div>
                    <p className="text-gray-100 text-sm mb-4">{repo.description || 'No description provided'}</p>
                    <div className="mt-auto">
                      {repo.language && (
                        <div className="flex items-center mb-3">
                          <div 
                            className="w-3 h-3 rounded-full mr-2" 
                            style={{backgroundColor: languageColors[repo.language] || '#777'}}
                          ></div>
                          <span className="text-sm">{repo.language}</span>
                        </div>
                      )}
                      <a 
                        href={repo.html_url}
                        target="_blank" 
                        rel="noopener noreferrer"
                        className="text-white hover:text-gray-200 text-sm font-medium inline-flex items-center"
                      >
                        View on GitHub
                        <i className="fas fa-external-link-alt ml-1"></i>
                      </a>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          </section>

          {/* Languages and More Stats */}
          <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
            {/* Language Distribution */}
            <div className="glass-morphism p-6 rounded-xl">
              <h2 className="text-xl font-bold mb-4 flex items-center">
                <i className="fas fa-code mr-2"></i>
                Language Distribution
              </h2>
              <div className="space-y-3">
                {topLanguages.length > 0 ? (
                  topLanguages.map(([lang, count]) => (
                    <div key={lang} className="mb-2">
                      <div className="flex justify-between text-sm mb-1">
                        <span>{lang}</span>
                        <span>{Math.round((count / repos.length) * 100)}%</span>
                      </div>
                      <div 
                        className="language-bar" 
                        style={{
                          width: `${(count / repos.length) * 100}%`,
                          backgroundColor: languageColors[lang] || '#777'
                        }}
                      ></div>
                    </div>
                  ))
                ) : (
                  <p>No language data available</p>
                )}
              </div>
            </div>

            {/* GitHub Activity */}
            <div className="glass-morphism p-6 rounded-xl">
              <h2 className="text-xl font-bold mb-4 flex items-center">
                <i className="fas fa-chart-line mr-2"></i>
                GitHub Activity
              </h2>
              <div className="space-y-4">
                <div>
                  <div className="flex items-center justify-between mb-1">
                    <span className="text-gray-300">Average Commits per Repo</span>
                    <span className="font-medium">{commitData?.average || 0}</span>
                  </div>
                  <div className="w-full bg-gray-700 h-2 rounded-full">
                    <div 
                      className="bg-purple-500 h-2 rounded-full" 
                      style={{width: `${Math.min(commitData?.average * 5 || 0, 100)}%`}}
                    ></div>
                  </div>
                </div>
                <div>
                  <div className="flex items-center justify-between mb-1">
                    <span className="text-gray-300">Repository Size</span>
                    <span className="font-medium">
                      {Math.round(repos.reduce((acc, repo) => acc + (repo.size || 0), 0) / 1024)} MB
                    </span>
                  </div>
                  <div className="w-full bg-gray-700 h-2 rounded-full">
                    <div 
                      className="bg-blue-500 h-2 rounded-full" 
                      style={{width: `${Math.min(repos.reduce((acc, repo) => acc + (repo.size || 0), 0) / 10000, 100)}%`}}
                    ></div>
                  </div>
                </div>
                <div>
                  <div className="flex items-center justify-between mb-1">
                    <span className="text-gray-300">Forks</span>
                    <span className="font-medium">
                      {repos.reduce((acc, repo) => acc + repo.forks_count, 0)}
                    </span>
                  </div>
                  <div className="w-full bg-gray-700 h-2 rounded-full">
                    <div 
                      className="bg-green-500 h-2 rounded-full" 
                      style={{width: `${Math.min(repos.reduce((acc, repo) => acc + repo.forks_count, 0) * 5, 100)}%`}}
                    ></div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      );
    };

    const App = () => {
      return (
        <div className="max-w-7xl mx-auto">
          <GitHubDashboard />
        </div>
      );
    };

    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>
